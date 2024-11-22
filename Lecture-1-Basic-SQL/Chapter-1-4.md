### 1. DBMS

- 데이터를 체계적으로 저장하고 관리하는 시스템

### 2. RDBMS

- 관계형 데이터베이스 관리 시스템
- SQL 문을 사용
- <-> 빅데이터를 다루는 데이터베이스: NoSQL

**주요 용어**

- **테이블(table)** = 관계(relation)
- **컬럼(column)** = 필드(field) = 속성(attribute)
- **로우(row)** = 레코드(record) = 튜플(tuple)
- Primary Key(기본키): 테이블에서 각 행(row)을 고유하게 식별하기 위해 사용되는 컬럼, 중복, NULL 허용 안함
- Foreign Key(외래키): 한 테이블의 컬럼이 다른 테이블의 Primary Key를 참조하도록 설정된 키, FK를 통해 다른 테이블의 행을 식별할 수 있음

### 3. SQL

- RDBMS에서 데이터를 관리하기 위해 사용되는 언어
- 데이터 정의 언어(DDL): 데이터 구조 정의
- 데이터 조작 언어(DML): 데이터 CRUD
- 데이터 제어 언어(DCL): 권한 설정, 데이터 무결성 처리 등 수행

### 4. MySQL, MySQL Workbench 설치

<br/><br/>

## CH4. SQL 기초 문법의 이해(데이터베이스 스키마 구성)

### 4.1. SQL로 데이터베이스 만들기

- 명령은 대문자, 변수는 소문자

```sql
// 데이터베이스 생성
CREATE DATABASE dbname;
CREATE SCHEMA dbname;

// 데이터베이스 목록 보기
SHOW DATABASES;

// 특정 데이터베이스 사용 시
USE dbname;

// 데이터베이스 삭제
DROP DATABASE dbname;
DROP DATABASE IF EXISTS dbname; // 해당 데이터베이스 이름이 없더라도 오류 발생 X
```

```sql
CREATE DATABASE grkim;
USE grkim;
SHOW DATABASES;
DROP DATABASE IF EXISTS grkim;
```

### 4.2. 테이블 선언을 위한 숫자 타입

- `INT` : 정수형 데이터 타입(4byte)
- `FLOAT` : 부동 소수형 데이터 타입(4byte)

**관련 옵션**

- `[UNSIGNED]` : 음수 값을 허용하지 않도록 설정
    - 예) `TINYINT`는 -128 ~ +127, `TINYINT UNSIGNED`는 0 ~ 255
- `[NOT NULL]` : 컬럼에 NULL 값을 허용하지 않도록 설정하는 제약 조건
- `[AUTO_INCREMENT]` : 새로운 레코드가 삽입될 때마다 특정 컬럼의 값을 자동으로 증가시키는 속성
    - 고유한 값을 생성하므로, 일반적으로 `PRIMARY KEY` 또는 `UNIQUE` 제약 조건과 함께 사용

```sql
// 기본 문법(CREATE TABLE 구문)
CREATE TABLE mytable(
	id INT UNSIGNED NOT NULL AUTO_INCREMENT,
    name VARCHAR(50),
    PRIMARY KEY(id)
);
```

### 4.3. 테이블 선언을 위한 문자/시간 타입

**문자 타입**

- `CHAR(n)` : 고정 길이 데이터 타입(n ≤ 255)
- `VARCHAR(n)` : 가변 길이 데이터 타입(n ≤ 65535)

**시간 타입**

- `DATE` : 날짜(YYYY-MM-DD) 형태의 기간 표현 데이터 타입(3byte)
- `DATETIME` : 날짜와 시간(YYYY-MM-DD hh:mm:ss) 형태
- `TIMESTAMP` : 1970-01-01 00:00:00 부터 시스템 현재 시간까지의 지난 시간을 초로 환산하여 숫자로 표현

```sql
CREATE TABLE mytable(
	name VARCHAR(50),
	ts DATE
);
```

**Primary Key 필드**

- NULL 값을 등록할 수 없고, 컬럼 내에서 유일한 값이어야 함
- 보통 NOT NULL(NULL 값 방지), AUTO_INCREMENT(유일함) 선언이 되어 있는 경우가 많음

```sql
CREATE TABLE mytable(
	컬럼명 데이터형,
	...
	PRIMARY KEY(컬럼명1, 컬럼명2, ...)
);
```

☀️ 실습 - 테이블 생성

```sql
CREATE DATABASE customer_db;
USE customer_db;
CREATE TABLE customer(
		no INT NOT NULL AUTO_INCREMENT,
    name CHAR(20) NOT NULL,
    age TINYINT,
    phone VARCHAR(20),
    email VARCHAR(30) NOT NULL,
    address VARCHAR(50),
    PRIMARY KEY (no)
);
```

### 4.4. 테이블 생성, 삭제, 수정 문법

**테이블 조회**

```sql
SHOW TABLES; // 해당 테이블이 존재하는지 확인
desc mytable; // 해당 테이블의 구조 확
```

**테이블 삭제**

```sql
DROP TABLE [IF EXISTS] mytable;
```

**테이블 구조 수정**

```sql
// 새로운 컬럼 추가
ALTER TABLE [테이블명] ADD COLUMN [추가할 컬럼명][추가할 컬럼 데이터형] 
ALTER TABLE mytable ADD COLUMN name VARCHAR(10) NOT NULL;

// 컬럼 타입 변경
ALTER TABLE [테이블명] MODIFY COLUMN [변경할 컬럼명][변경할 컬럼 타입]
ALTER TABLE mytable MODIFY DOLUMN name VARCHAR(20) NOT NULL;

// 컬럼 이름 번경
ALTER TABLE [테이블명] CHANGE COLUMN [기존 컬럼 명][변경할 컬럼 명][변경할 컬럼 타입]
ALTER TABLE mytable CHANGE COLUMN name age INT NOT NULL;

// 컬럼 삭제
ALTER TABLE [테이블명] DROP COLUMN [삭제할 컬럼 명]
ALTER TABLE mytable DROP COLUMN age;
```

☀️ 실습 - 테이블  생성, 조회

```sql
CREATE DATABASE dave;
USE dave;
CREATE TABLE mytable (
	id INT UNSIGNED NOT NULL AUTO_INCREMENT,
	name VARCHAR(50) NOT NULL,
	modelnumber VARCHAR(15) NOT NULL,
	series VARCHAR(30) NOT NULL,
	PRIMARY KEY(id)
);
desc mytable;

ALTER TABLE mytable MODIFY COLUMN name VARCHAR(20) NOT NULL;
ALTER TABLE mytable CHANGE COLUMN modelnumber model_num VARCHAR(10) NOT NULL;
ALTER TABLE mytable CHANGE COLUMN series model_type VARCHAR(10) NOT NULL;

DROP TABLE mytable;

CREATE TABLE model_info(
	id INT NOT NULL AUTO_INCREMENT,
	name VARCHAR(20) NOT NULL,
	model_num VARCHAR(10) NOT NULL,
	model_type VARCHAR(10) NOT NULL,
	PRIMARY KEY (id)
);
desc model_info;
```
