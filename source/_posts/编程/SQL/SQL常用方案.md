---
title: SQL常用语句
date: 2018/4/4 16:14:47
tags:
categories: [编程, SQL]
---
1. WITH---AS用法
```
WITH user_info AS
(select *
    from (SELECT u1.*, ROWNUM RN
            FROM (SELECT * FROM owmdom.tm_mdom_internal_user) u1
           WHERE ROWNUM <= 10)
   WHERE RN >= 0)
select ui.I_USER_CODE, ui.I_USER_NAME, ui.MOBILE, ui.EMAIL
  from user_info ui
  left join (select u.I_USER_CODE,
                    wm_concat(ops.int_position_code || ':' ||
                              ops.int_position_name) AS pos_info
               from owmdom.tr_mdom_in_user_org_position uog,
                    owmdom.tr_mdom_in_org_position      op,
                    owmdom.tm_mdom_int_position         ops,
                    user_info                           u
              where uog.i_org_postion_id = op.i_org_postion_id
                and ops.int_position_id = op.int_position_id
                and uog.i_user_id = u.i_user_id
             --and u.I_USER_CODE like 'apptest%'
              group by u.I_USER_CODE) uip
    on ui.I_USER_CODE = uip.I_USER_CODE;
```

2. FOR循环
```
BEGIN
  FOR a in (SELECT m.MERCHANDISE_CODE as merchandiseCode FROM (SELECT * FROM TT_POL_OVERSTOCK WHERE BATCH_NO IN (TO_CHAR(add_months(sysdate, -2),'yyyyMM'), TO_CHAR(add_months(sysdate, -1),'yyyyMM'), TO_CHAR(sysdate,'yyyyMM'))) o LEFT JOIN TT_POL_MERCHANDISE m ON o.ASC_CODE=m.SELLER_CODE AND o.PART_CODE=m.PART_CODE WHERE AUTO_REMOVE_FLAG=1) LOOP
    UPDATE TT_POL_MERCHANDISE SET AUTO_REMOVE_FLAG=0, MERCHANDISE_STATUS=1 WHERE MERCHANDISE_CODE=a.merchandiseCode;
  END LOOP;
END;
```
3. INSERT多条语句
```
INSERT INTO TABLE_NAME (SELECT * FROM TABLE_NAME1)
INSERT INTO TABLE_NAME (字段1) (字段1值)
```

4. 查询出col中符合某些条件的结果
例如查询某个ascCode其batchNo中在近三个月中未出现的记录
```
WITH T AS (SELECT ASC_CODE, PART_CODE FROM TT_POL_OVERSTOCK WHERE BATCH_NO IN (TO_CHAR(add_months(sysdate, -2),'yyyyMM'), TO_CHAR(add_months(sysdate, -1),'yyyyMM'), TO_CHAR(sysdate,'yyyyMM')))
    SELECT * FROM TT_POL_OVERSTOCK o WHERE
      BATCH_NO NOT IN (TO_CHAR(add_months(sysdate, -2),'yyyyMM'), TO_CHAR(add_months(sysdate, -1),'yyyyMM'), TO_CHAR(sysdate,'yyyyMM'))
      AND (o.ASC_CODE, o.PART_CODE) NOT IN (SELECT T.ASC_CODE, T.PART_CODE FROM T)
```
