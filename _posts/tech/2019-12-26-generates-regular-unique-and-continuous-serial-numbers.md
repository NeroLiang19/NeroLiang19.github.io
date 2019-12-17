---
layout: post
title: Oracle procedure递归方法生成有规律唯一不重复且连续的流水号(处理字母数字)
category: 技术
tags: [Oracle,Procedure]
keywords: Oracle,procedure,流水号
description:
---

# 由于业务需求,产品编号某些位数是字母,字母也是需要连续的，前2位固定,，不能出现"E","I","O"。比如客户指定产品编号类似是“0A00A0000A00” 字母位数是字母，不够进一，"A"--->"B" ，而数字进一就简单。

## 这里有两种方法。

### 1：拆分位数。

由于前2位固定。则可以将   0A00A0000A00拆分成：“00”,"A","0000","A","00" 5个单独的流水号。

然后规则是将排在最后的“00” +1，那么就是"01" ，不需要进一，则再和前面的拼凑起来，最后就是“0A00A0000A01”. 并插入到另一个数据库。

数据库HANDLE1_SERIAL_NO_Generate 是触发数据库，而拿生成好的数据是在另一个数据库HANDLE1_SERIAL_NO(表结构一样)

表结构：

```
-- Create table
create table HANDLE1_SERIAL_NO
(
  SERIAL_NO    VARCHAR2(20) not null,
  MACHINE_NO       VARCHAR2(20) not null,
  TIME    DATE not null,
  SERIAL_COUNT NUMBER--需要生成多少个流水号
)
-- Create/Recreate primary, unique and foreign key constraints 
alter table HANDLE1_SERIAL_NO_PROD
  add constraint PK_HANDLE1_SERIAL_NO primary key (SERIAL_NO)
```

以下是存储过程代码

```
Create Or Replace Trigger TRG_HANDLE1_SERIAL_NO_GENERATE
  Before Insert Or Update On HANDLE1_SERIAL_NO_GENERATE For Each Row
Declare
  maxSerialNO   varchar2(20);
  currSerialNO  varchar2(20);
  SN            number;
  serialCount   number;
  serialNoChar1 varchar2(5);
  serialNoChar2 varchar2(5);
  serialNoChar3 varchar2(5);
  serialNoChar4 varchar2(5);
  serialNoChar5 varchar2(5);
Begin
  if inserting then
    if(:new.serial_no is null or length(:new.serial_no) < 12) then
      RAISE_APPLICATION_ERROR(-20998,'ERROR: the serial_no is illegal!');
    end if;

    serialCount:=0;
    while serialCount<:new.serial_count loop
      --if upper(substr(:new.serial_no,3,10)) = '00A0000A00' then
        select max(serial_no) into maxSerialNO from HANDLE1_SERIAL_NO;
        if maxSerialNO is null then
           currSerialNO := :new.serial_no;
        else
          serialNoChar1:=substr(maxSerialNO,3,2);
          serialNoChar2:=substr(maxSerialNO,5,1);
          serialNoChar3:=substr(maxSerialNO,6,4);
          serialNoChar4:=substr(maxSerialNO,10,1);
          serialNoChar5:=substr(maxSerialNO,11,2);
          if to_number(serialNoChar5)<99 then
            SN:=to_number(serialNoChar5)+1;
            serialNoChar5 := lpad(to_char(SN),2,'0');
          else
            serialNoChar5:='00';
            if ascii(serialNoChar4)+1 >ascii('Z') then
               serialNoChar4:='A';
               if to_number(serialNoChar3) +1>9999 then
                 serialNoChar3 := '0000';
                 if ascii(serialNoChar2)+1 >ascii('Z') then
                   serialNoChar2:='A';
                   serialNoChar1:=lpad(to_char(to_number(serialNoChar1)+1),2,'0');
                 else
                   if (ascii(serialNoChar2)+1 =ascii('E') or ascii(serialNoChar2)+1 =ascii('I') or ascii(serialNoChar2)+1 =ascii('O')) then
                     serialNoChar2:=chr(ascii(serialNoChar2)+2);
                   else
                     serialNoChar2:=chr(ascii(serialNoChar2)+1);
                   end if;
                 end if;
               else
                 serialNoChar3 := lpad(to_char(to_number(serialNoChar3) +1),4,'0');
               end if;
            else
              if (ascii(serialNoChar4)+1 =ascii('E') or ascii(serialNoChar4)+1 =ascii('I') or ascii(serialNoChar4)+1 =ascii('O')) then
                serialNoChar4 :=chr(ascii(serialNoChar4)+2);
              else
                serialNoChar4 :=chr(ascii(serialNoChar4)+1);
              end if;
            end if;
          end if;
         currSerialNO := substr(maxSerialNO,1,2)|| serialNoChar1||serialNoChar2||serialNoChar3||serialNoChar4||serialNoChar5;
        end if;
        --RAISE_APPLICATION_ERROR(-20998,currSerialNO);
        Insert into HANDLE1_SERIAL_NO(serial_no,tester,test_time) values(currSerialNO,:new.tester,:new.test_time);
        :new.serial_no := currSerialNO;
        serialCount:=serialCount+1;
    end loop;
  end if;
  
  exception
     when others then
       RAISE_APPLICATION_ERROR(-20998,'ERROR:Insert serial no failed in DB!');
End;
```


### 2：递归方法

我发现第一种方法虽然能够达到客户需求，但是不利于以后的扩展，并且整个存储过程比较臃肿。于是就是改进该方法，使用递归函数。

目前是前2位固定，但是这种方法的流水号的长度是可变的，只是前2为固定，并且字母所在的位也可以随意放置，比起第一种，以后如果流水号格式改变成”0A0000000000AAA“,第一种方法需要大变动，而这种方法可以继续使用(前2为固定"0A").

当然读者可以把前2位固定的可以变成参数传入，修改下函数，这样就更加灵活了。

触发器：


```
Create Or Replace Trigger HANDLE1_SERIAL_NO_Generate
  Before Insert Or Update On HANDLE1_SERIAL_NO_Generate For Each Row

declare
maxSerialNo varchar2(20);
SerialNoType varchar2(10);
currSerialNO varchar2(20);

begin
  /* serial no length can not less than 3 */
  if length(:new.serial_no)<4 then
    RAISE_APPLICATION_ERROR(-20999,'ERROR:serial no length is less than 3!');
  end if;
  
  if :new.serial_count>100 then 
    RAISE_APPLICATION_ERROR(-30001,'ERROR:!serial_count');
  end if;

  if inserting then
    /* serial_no:0A00A0000A00,fixed top 2 */
   for i in 1..:new.serial_count loop
     SerialNoType:=substr(:new.serial_no,1,2);
     select max(serial_no) into maxSerialNo from HANDLE1_SERIAL_NO where serial_no like SerialNoType || '%' and length(serial_no)=length(:new.serial_no);
   if maxSerialNO is null then
     currSerialNO:= :new.serial_no;
   else
     currSerialNO:=fun_GetHandleSerialNO(maxSerialNo);
     if instr(currSerialNO,'not enough')>0 then
       RAISE_APPLICATION_ERROR(-20302,'ERROR:SerialNo is not enough!');
     end if;
     if instr(currSerialNO,'error')>0 then
       RAISE_APPLICATION_ERROR(-20303,'error get serialno function!');
     end if;
    end if;

    Insert into HANDLE1_SERIAL_NO(serial_no,tester,test_time) values(currSerialNO,:new.tester,:new.test_time);
    :new.serial_no:= currSerialNO;
    end loop;
   end if;

  exception
     when others then
       RAISE_APPLICATION_ERROR(-20998,'ERROR:Insert serial no failed in DB!');
end;

```

递归函数：

```
create or replace function fun_GetHandleSerialNO(SerialNo varchar2) return varchar2 is

SingleChar varchar2(5);
SingleCharacter varchar2(5);
SingleNum number;
NewSerialNo varchar2(20);

begin

  if substr(SerialNo,3,1)='Z' then
    return 'error digit is not enough';
  end if;

  SingleChar:=substr(SerialNo,length(SerialNo),1);
  /*ascii('0') = 48, ascii('9') = 57
    ascii('A') = 65, ascii('Z') = 90 */

  /*Number Type*/
  if ascii(SingleChar)>=48 and ascii(SingleChar)<=57 then
    SingleNum:=to_number(SingleChar)+1;
    if SingleNum>9 then
      NewSerialNo:=fun_GetHandleSerialNO(substr(SerialNo,1,length(SerialNo)-1));
      return NewSerialNo || '0';
    else
     return substr(SerialNo,1,length(SerialNo)-1) || SingleNum;
    end if;   
  /* Character Type */
  else 
    if ascii(SingleChar)>=65 and ascii(SingleChar)<=90 then
      SingleCharacter:=chr(ascii(SingleChar)+1);
      /* filtered character 'E','I','O' */
      if (SingleCharacter ='E' or SingleCharacter ='I' or SingleCharacter ='O') then
         SingleCharacter:=chr(ascii(SingleChar)+2);
      end if;
      if SingleCharacter>'Z' then
        NewSerialNo:=fun_GetHandleSerialNO(substr(SerialNo,1,length(SerialNo)-1));
        return NewSerialNo || 'A';
      else
       return substr(SerialNo,1,length(SerialNo)-1) || SingleCharacter;
      end if;
    end if;   
  end if;
EXCEPTION
  WHEN others THEN
    return 'error get serialno function';
end;
```

使用方法：

```
insert into handle1_serial_no_generate
  (serial_no, tester, test_time, serial_count)
values
  ('0A00A0000A00', 'test1', sysdate, 10);
```

结果：

handle_serial_no_generate:

0A00AAAAA0009 test1 2019/5/22 11:39:2810

handle_serial_no:

0A00AAAAA0000 test1 2019/5/22 11:39:28
0A00AAAAA0001 test1 2019/5/22 11:39:28
0A00AAAAA0002 test1 2019/5/22 11:39:28
0A00AAAAA0003 test1 2019/5/22 11:39:28
0A00AAAAA0004 test1 2019/5/22 11:39:28
0A00AAAAA0005 test1 2019/5/22 11:39:28
0A00AAAAA0006 test1 2019/5/22 11:39:28
0A00AAAAA0007 test1 2019/5/22 11:39:28
0A00AAAAA0008 test1 2019/5/22 11:39:28
0A00AAAAA0009 test1 2019/5/22 11:39:28


当遇到"E","I"."O"自动进一,长度可变。

handle_serial_no_generate:

0A00AAAAA0000AAL test1 2019/5/22 11:48:0310

handle_serial_no:

0A00AAAAA0000AAA test1 2019/5/22 11:48:03

0A00AAAAA0000AAB test1 2019/5/22 11:48:03
0A00AAAAA0000AAC test1 2019/5/22 11:48:03
0A00AAAAA0000AAD test1 2019/5/22 11:48:03
0A00AAAAA0000AAF test1 2019/5/22 11:48:03
0A00AAAAA0000AAG test1 2019/5/22 11:48:03
0A00AAAAA0000AAH test1 2019/5/22 11:48:03
0A00AAAAA0000AAJ test1 2019/5/22 11:48:03
0A00AAAAA0000AAK test1 2019/5/22 11:48:03
0A00AAAAA0000AAL test1 2019/5/22 11:48:03

