## 1 ��ͼ

> ��ͼ����û������, ���ݴ洢�ڱ���

- ʹ����ͼ���������û���ĳЩ���ݵķ��ʣ����Լ򻯲�ѯ

- ����ͨ����ͼȥ�޸ı������

  ���Խ���ͼ����Ϊֻ������:with read only

  `create or replace view vm_emp as select * from emp with read only`

- ������ͼ

  ```
  create view vm_emp as select * from emp;
  create or replace view vm_emp as select * from emp;
  create or replace view vm_emp as select deptno, empno, ename from emp where deptno=10;
  select view_name from user_views;
  ```

- ɾ����ͼ

  `drop view vm_emp`

## 2 ����

> ʹ����������Ŀ������߲�ѯ��Ч��

- ������ԭ��

  - �ڲ�ѯ��ʱ��where��������Ҫʹ�ô���������ʱ����У�oracle�Ȳ�ѯ������
  - �����������ҵ����е�ֵ��Ӧ��rowid���ҵ�rowid�ٴӱ��и���rowid�ҵ���һ�м�¼

- ������`create index idx_emp on emp(empno)`

- ɾ����`drop index idx_emp_bak`

- ��ѯ����������

  `select index_name from user_indexes`

  `select xxxx_name from user_xxxxs`

## 3 ����

> ���ڱ������Ҫ���Ƿǿ���Ψһ�ģ�Ϊ�˱�֤�����Ƿǿպ�Ψһ�ģ�����ʹ������

- �������У�` create sequence seq_mytest`
- ɾ�����У�`drop sequence seq_mytest`
- ���е�����
  - currval �� nextval�����ǵ�һ��ʹ�õ�ʱ����Ҫȡnextval��ֵ

## 4 ͬ���

> ͬ��ʾ��Ǳ���

- ͬ���ʹ�õĳ���

  - �û�1�����scott�û���emp����Ҫscott�û����û�1������emp���Ȩ��

    `grant select on emp to �û�1`

  - Ȼ��ʹ���û�1�û���¼oracle

    `sqlplus �û�1/�û�1@oracle`

    `select * from scott.emp`

    Ϊ���ڷ���scott.emp���ʱ������ʹ��scott.emp�����Ը�scott.emp����ͬ���

- ����ͬ��ʣ�` create synonym emp for scott.emp`
- ɾ��ͬ��ʣ�`drop synonym emp`

```
����һ���µ�oracle�û�:
	//ʹ��sys�û������µ��û��͸�������û����Ȩ��
	create user New identified by New;
	grant connect, resource to New;
	grant create synonym to New;
```

