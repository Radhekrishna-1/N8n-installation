## 3. PostgreSQL Database Setup

If you need to create the database and user on your external PostgreSQL server specifically for n8n, here are the required SQL commands along with the correct permissions.

### Connect to your PostgreSQL Instance
First, connect to your database as a superuser (like `postgres`):
```bash
sudo -u postgres psql
# OR if connecting remotely:
# psql -h your_db_host -U postgres
```

### Run these SQL Commands
Run the following inside the `psql` prompt (replace `your_secure_db_password` with a strong password):

```sql
-- 1. Create a dedicated user for n8n
CREATE USER n8n_user WITH PASSWORD 'your_secure_db_password';

-- 2. Create the n8n database, owned by the new user
CREATE DATABASE n8n OWNER n8n_user;

-- 3. Grant all privileges on the database to the new user
GRANT ALL PRIVILEGES ON DATABASE n8n TO n8n_user;

-- 4. Connect to the new database to set specific schema permissions
\c n8n

-- 5. Grant usage and creation rights on the public schema
GRANT ALL ON SCHEMA public TO n8n_user;
```

### Why these permissions?
* **CREATE USER & CREATE DATABASE**: n8n needs its own isolated space. Giving it owner rights over its specific database ensures it doesn't interfere with other applications.
* **GRANT ALL ON SCHEMA public**: When n8n starts up for the very first time, it has to automatically create dozens of tables and indexes (this is called database migration). This permission allows it to build its own structure cleanly.

After running these commands, update your `.env` file with `n8n`, `n8n_user`, and the password you set.
