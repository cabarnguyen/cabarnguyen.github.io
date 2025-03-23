---
title: "Cockroachdb programing"
date: 2025-03-23 00:00:00 +0800
categories: [Cockroach]
tags: [cockroach, connection]
---

## Connection pool

A connection pool is a set of connections
 that the application can reuse. You avoid the overhead of constantly creating and
 destroying connections, and you can control the maximum amount of concurrency
 hitting the database.

The Python driver psycopg2 includes a built-in connection pool that we can easily
 configure as follows
 ```python
 import psycopg2
 from psycopg2 import pool
 def main():
  if ((len(sys.argv)) !=2):
  sys.exit("Error:No URL provided on command line")
  uri=sys.argv[1]
  pool= psycopg2.pool.ThreadedConnectionPool(10, 40, uri)
  # min connection=10, max=40
  connection = pool.getconn()
 ```

## Working with ORM Frameworks

### Using SQLAlchemy with CockroachDB

SQLAlchemy is a popular ORM framework that can be used with CockroachDB. Below is an example of how to configure SQLAlchemy to connect to a CockroachDB cluster:

```python
from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker

# Replace with your CockroachDB connection URI
DATABASE_URI = "cockroachdb://<username>:<password>@<host>:<port>/<database>?sslmode=require"

# Create the SQLAlchemy engine
engine = create_engine(DATABASE_URI)

# Create a session factory
Session = sessionmaker(bind=engine)

# Example usage
def main():
    session = Session()
    try:
        # Perform database operations
        print("Connected to CockroachDB!")
    finally:
        session.close()

if __name__ == "__main__":
    main()
```

For more details, refer to the [official CockroachDB documentation](https://cockroa.ch/3j6S8fb).

**Using rye**
```
rye add psycopg2-binary sqlalchemy sqlalchemy-cockroachdb
```

Sample code:
```python
import sys
from sqlalchemy import create_engine, Column, String, DECIMAL, TIMESTAMP, ForeignKey, Index, CheckConstraint
from sqlalchemy.dialects.postgresql.base import UUID
from sqlalchemy.orm import sessionmaker, DeclarativeBase

# Use DeclarativeBase (newer SQLAlchemy versions)
class Base(DeclarativeBase):
    pass

class Ride(Base):
    __tablename__ = "rides"
    __table_args__ = (
        Index("rides_auto_index_fk_city_ref_users", "city", "rider_id"),
        Index("rides_auto_index_fk_vehicle_city_ref_vehicles", "vehicle_city", "vehicle_id"),
        CheckConstraint("vehicle_city = city", name="check_vehicle_city_city"),
    )

    id = Column(UUID, primary_key=True)
    city = Column(String, primary_key=True, nullable=False)
    vehicle_city = Column(String, nullable=True)
    rider_id = Column(UUID, ForeignKey("users.id", ondelete="CASCADE"), nullable=True)
    vehicle_id = Column(UUID, ForeignKey("vehicles.id", ondelete="CASCADE"), nullable=True)
    start_address = Column(String, nullable=True)
    end_address = Column(String, nullable=True)
    start_time = Column(TIMESTAMP, nullable=True)
    end_time = Column(TIMESTAMP, nullable=True)
    revenue = Column(DECIMAL(10, 2), nullable=True)


 
def main():
    # if len(sys.argv) != 2:
    #     sys.exit("Error: No URL provided on the command line.")

    # uri = sys.argv[1]

    # # ✅ Use CockroachDB dialect
    # if not uri.startswith("cockroachdb://"):
    #     sys.exit("Error: The database URL must use 'cockroachdb://' instead of 'postgresql://'")

    uri = 'cockroachdb://root@172.26.176.1:26257/movr?sslmode=disable'


    engine = create_engine(uri)
    SessionLocal = sessionmaker(bind=engine)

    ride_id = "54fdf3b6-45a1-4c00-8000-0000000000a6"

    with SessionLocal() as session:


        try:
            session.begin_nested()  # ✅ CockroachDB best practice for transactions
            
            ride = session.query(Ride).filter(Ride.id == ride_id).first()
            
            session.commit()  # ✅ Commit transaction
            if ride:
                print(f"✅ Ride Found: {ride.id}, {ride.city}, {ride.start_address} → {ride.end_address}, Revenue: {ride.revenue}")
            else:
                print("❌ No ride found with the given ID.")

        except Exception as e:
            session.rollback()  # ❌ Rollback on error
            print("⚠️ Error:", e)

        finally:
            session.close()  # ✅ Close session properly

if __name__ == "__main__":
    main()

```
