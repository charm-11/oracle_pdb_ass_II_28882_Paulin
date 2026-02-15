# Oracle Pluggable Database (PDB) Assignment II

| **Student ID** | 28882
| **First Name** | Cyomoro Ngabo Paulin
| **Course** | Database Administration 
| **Assignment** | Oracle PDB Management – Assignment II 

---

## Overview

This assignment demonstrates the creation, management, and administration of Oracle Pluggable Databases (PDBs) within a Multitenant Container Database (CDB) environment. All tasks were completed using Oracle SQL*Plus and Oracle Enterprise Manager (OEM).

---

## Task 1: Creating and Opening a Pluggable Database

### Objective
Create a new PDB named `PA__pdb_28882` with a dedicated admin user, then verify its status.

### Steps Performed

**1. Connect as SYSDBA**
```sql
sqlplus / as sysdba
```

**2. Create the PDB**
```sql
CREATE PLUGGABLE DATABASE PA__pdb_28882]
ADMIN USER pdb_admin IDENTIFIED BY [password]
FILE_NAME_CONVERT = ('pdbseed', 'PA__pdb_28882]');
```

**3. Open the PDB**
```sql
ALTER PLUGGABLE DATABASE PA__pdb_28882] OPEN;
```

**4. Verify PDB Status**
```sql
SELECT name, open_mode FROM v$pdbs;
```

**5. Connect to the New PDB and Verify Admin User**
```sql
ALTER SESSION SET CONTAINER = pdb_28882_PA;
SELECT username FROM dba_users WHERE username = 'PDB_ADMIN';
```

### Expected Outcome
The PDB is created and opens in `READ WRITE` mode. The admin user `PDB_ADMIN` is visible in `dba_users`.

---

## Task 2: Creating and Dropping a Temporary PDB

### Objective
Create a temporary PDB, confirm both PDBs coexist, then drop the temporary one.

### Steps Performed

**1. Create the Temporary PDB**
```sql
CREATE PLUGGABLE DATABASE [PA_pdb_28882]
ADMIN USER temp_admin IDENTIFIED BY 1234
FILE_NAME_CONVERT = ('pdbseed', '[PA_pdb_28882');
```

**2. Open the Temporary PDB**
```sql
ALTER PLUGGABLE DATABASE [PA__delete_pdb_28882] OPEN;
```

**3. Verify Both PDBs Exist**
```sql
SELECT name, open_mode FROM v$pdbs;
```
At this stage, both PA_pdb_28882` and `[PA_delete_pdb_28882]` should appear in `READ WRITE` mode alongside `PDB$SEED`.

**4. Close and Drop the Temporary PDB**
```sql
ALTER PLUGGABLE DATABASE [PA__delete_pdb_28882] CLOSE IMMEDIATE;
DROP PLUGGABLE DATABASE [PA__delete_pdb_28882] INCLUDING DATAFILES;
```

**5. Confirm Deletion**
```sql
SELECT name, open_mode FROM v$pdbs;
```

### Expected Outcome
The temporary PDB is removed; only `PDB$SEED` and `pdb_28882_PA` remain.

---

## Task 3: Oracle Enterprise Manager (OEM)

### Objective
Access OEM to verify PDB status and confirm the admin username through the graphical interface.

### Access Details

| Property | Value |
|---|---|
| **URL** | `https://localhost:5500/em` |
| **Login** | `SYS` as `SYSDBA` |

### Steps Performed

1. Opened a browser and navigated to `https://localhost:5500/em`
2. Logged in with `SYS` credentials using SYSDBA role
3. Navigated to the **Container Database** section to view PDB status
4. Confirmed `pdb_[studentId]_[firstname]` is listed with `OPEN` status
5. Verified the admin username under the PDB user management panel

---

## Challenges and Solutions

| Challenge | Solution | Learning |
|---|---|---|
| "Insufficient privileges" error when opening PDB | Reconnected as SYSDBA using `sqlplus / as sysdba` | Always verify SYSDBA connection before performing administrative tasks |
| PDB not visible after creation | Ran `SELECT name, open_mode FROM v$pdbs;` to confirm CDB context | Must be connected to the root CDB (`CDB$ROOT`) to see all PDBs |

---

## Key SQL Commands Reference

```sql
-- View all PDBs in the CDB
SELECT name, open_mode, con_id FROM v$pdbs;

-- Switch container session
ALTER SESSION SET CONTAINER = <pdb_name>;

-- Open a PDB
ALTER PLUGGABLE DATABASE <pdb_name> OPEN;

-- Close a PDB
ALTER PLUGGABLE DATABASE <pdb_name> CLOSE IMMEDIATE;

-- Drop a PDB and its datafiles
DROP PLUGGABLE DATABASE <pdb_name> INCLUDING DATAFILES;

-- Save open state across restarts
ALTER PLUGGABLE DATABASE <pdb_name> SAVE STATE;
```

---





*Assignment completed as part of the Database Administration curriculum.*
