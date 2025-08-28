# PostgreSQL Publication & Subscription Automation Script

This script automates the setup of **PostgreSQL logical replication** (Publication & Subscription) across multiple database nodes.  

It detects the local node automatically, creates a **publication** on it, and then sets up **subscriptions** to peer nodes.

---

## 📌 Script Details

- **Database**: `<Database_name>`  
- **User**: `<Database_username>`  
- **Password**: `<Database_password>`  
- **Port**: `5432`  

Nodes considered in this setup:
- `db1` → `<Replace_VM_Host_name>`
- `db2` → `<Replace_VM_Host_name>`
- `db3` → `<Replace_VM_Host_name>`

---

## 📜 Script

```bash
#!/bin/bash
# Script to automate PostgreSQL Publication & Subscription setup
# Run this on each DB node

# ==== CONFIGURATION ====
DB_NAME="<Database_name>"
DB_USER="<Database_username>"
DB_PASS="<Database_password>"
DB_PORT=5432

# Identify local node (shortname: db1, db2, db3)
LOCAL_HOST=$(hostname -f)
case $LOCAL_HOST in
  *techno-db-1*) NODE_NAME="db1" ;;
  *techno-db-2*) NODE_NAME="db2" ;;
  *techno-db-3*) NODE_NAME="db3" ;;
  *) echo "Unknown host: $LOCAL_HOST"; exit 1 ;;
esac

PUB_NAME="pub_${NODE_NAME}"

# Peers (short name + host)
declare -A PEERS
PEERS[db1]="<Replace_VM_Host_name>"
PEERS[db2]="<Replace_VM_Host_name>"
PEERS[db3]="<Replace_VM_Host_name>"

# ==== SCRIPT STARTS ====
echo "--------------------------------------------------"
echo "Setting up Publication and Subscriptions on $LOCAL_HOST ($NODE_NAME)"
echo "Database: $DB_NAME | User: $DB_USER | Port: $DB_PORT"
echo "--------------------------------------------------"

# Create publication
echo "Creating publication: $PUB_NAME ..."
PGPASSWORD=$DB_PASS psql -h $LOCAL_HOST -U $DB_USER -d $DB_NAME -p $DB_PORT \
  -c "DROP PUBLICATION IF EXISTS $PUB_NAME;" 2>/dev/null

PGPASSWORD=$DB_PASS psql -h $LOCAL_HOST -U $DB_USER -d $DB_NAME -p $DB_PORT \
  -c "CREATE PUBLICATION $PUB_NAME FOR ALL TABLES;"

# Create subscriptions for peers
for peer_node in "${!PEERS[@]}"; do
  peer_host=${PEERS[$peer_node]}
  if [[ "$peer_node" != "$NODE_NAME" ]]; then
    SUB_NAME="sub_${NODE_NAME}_from_${peer_node}"
    PUB_PEER="pub_${peer_node}"

    echo "Creating subscription $SUB_NAME to $peer_node ($peer_host) ..."
    PGPASSWORD=$DB_PASS psql -h $LOCAL_HOST -U $DB_USER -d $DB_NAME -p $DB_PORT \
      -c "DROP SUBSCRIPTION IF EXISTS $SUB_NAME;" 2>/dev/null

    PGPASSWORD=$DB_PASS psql -h $LOCAL_HOST -U $DB_USER -d $DB_NAME -p $DB_PORT \
      -c "CREATE SUBSCRIPTION $SUB_NAME CONNECTION 'host=$peer_host port=$DB_PORT user=$DB_USER password=$DB_PASS dbname=$DB_NAME sslmode=require' PUBLICATION $PUB_PEER WITH (copy_data = false, synchronous_commit = 'off');"
  fi
done

# Check publications
echo "--------------------------------------------------"
echo "Checking publications on $NODE_NAME ..."
PGPASSWORD=$DB_PASS psql -h $LOCAL_HOST -U $DB_USER -d $DB_NAME -p $DB_PORT \
  -c "SELECT pubname FROM pg_publication;"

# Check subscriptions
echo "--------------------------------------------------"
echo "Checking subscriptions on $NODE_NAME ..."
PGPASSWORD=$DB_PASS psql -h $LOCAL_HOST -U $DB_USER -d $DB_NAME -p $DB_PORT \
  -c "SELECT subname, subenabled, subpublications, subconninfo FROM pg_subscription;"

echo "--------------------------------------------------"
echo "Replication setup complete on $NODE_NAME ($LOCAL_HOST)"
