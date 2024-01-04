� JDBC � � ������ Java-���������� ���������� ��������� �������� ���������� Batch Update, ������ �� ������� ����� ���� ����������� � ����������� � ����������� �� ���������� ���������� � ������� ������. ���������� �������� �� ���:

### 1. ������������� Statement

���� ����� �������� ��� ���������� ��������� ���������� SQL-�������� ��� ����������.

**������:**
```java
Statement statement = connection.createStatement();

for (String query : queries) {
    statement.addBatch(query);
}

int[] results = statement.executeBatch();
```

### 2. ������������� PreparedStatement

���� ����� ����� �������� ��� ���������� ��������� �������� � �����������, ��� ������ ������ ����������� � ������� ���������� ���� ����������.

**������:**
```java
PreparedStatement ps = connection.prepareStatement("INSERT INTO my_table (column1, column2) VALUES (?, ?)");

for (MyObject obj : objects) {
    ps.setString(1, obj.getColumn1());
    ps.setString(2, obj.getColumn2());
    ps.addBatch();
}

int[] results = ps.executeBatch();
```

### 3. Spring JdbcTemplate

`JdbcTemplate` � Spring ��������� ��������� ��������� Batch Update, �������� � ��������� Spring-����������.

**������:**
```java
jdbcTemplate.batchUpdate("INSERT INTO my_table (column1, column2) VALUES (?, ?)",
    new BatchPreparedStatementSetter() {
        public void setValues(PreparedStatement ps, int i) throws SQLException {
            MyObject obj = objects.get(i);
            ps.setString(1, obj.getColumn1());
            ps.setString(2, obj.getColumn2());
        }

        public int getBatchSize() {
            return objects.size();
        }
    });
```

### 4. NamedParameterJdbcTemplate

���� ����� ������ ��� ����� ������� �������� � ������������ �����������, ��� �������� ���������� � �������� ����.

**������:**
```java
NamedParameterJdbcTemplate namedParameterJdbcTemplate = new NamedParameterJdbcTemplate(dataSource);

List<SqlParameterSource> parameters = new ArrayList<>();
for (MyObject obj : objects) {
    SqlParameterSource param = new MapSqlParameterSource()
        .addValue("column1", obj.getColumn1())
        .addValue("column2", obj.getColumn2());
    parameters.add(param);
}

int[] results = namedParameterJdbcTemplate.batchUpdate("INSERT INTO my_table (column1, column2) VALUES (:column1, :column2)", 
    parameters.toArray(new SqlParameterSource[0]));
```

### 5. ������������� ORM Frameworks

ORM-����������, ����� ��� Hibernate ��� JPA, ����� ������������ Batch Update, ����������� ����� ��������������� � ��������-��������������� ������.

**������ � Hibernate:**
```java
Session session = sessionFactory.openSession();
Transaction tx = session.beginTransaction();

for ( int i=0; i<objects.size(); i++ ) {
    MyObject obj = objects.get(i);
    session.save(obj);
    if ( i % 50 == 0 ) { // 50, ��� ������ batch
        session.flush();
        session.clear();
    }
}

tx.commit();
session.close();
```

### ����� �������

����� ����������� ������ ������� �� ���������� ��������:

- **��������� ��������:** ��� ������� �������� � ���������� ���������� ����� ������������ `PreparedStatement` ��� `NamedParameterJdbcTemplate`.
- **������������������:** � ����������� ������� ������������� `PreparedStatement` ����� ����������� ������� � ����� ������ ������������������.
- **���������� � ������������:** ���� ���� ���������� ��� ���������� Spring ��� ORM-���������, ������������� ��������������� ������������ ����� ���� ����� ������� � ������������.

Batch Update � ��� ������ ���������� ��� ����������� ������������������ ��� ������, �������� ��� ������������� ��������� �������� ���������� ��������.