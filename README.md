# NoSQL Postgres with FerretDB, Fly.io, and Supabase

[FerretDB](https://www.ferretdb.com/) is an awesome open source projects using JSONB to implement MongoDB compatability on top of Postgres!

## Prerequisites

- A hosted Supabase project. Don't have one yet? -> [database.new](https://database.new)
- Docker or [Orbstack](https://orbstack.dev/)

## Run locally with Docker

```bash
# Expose required env vars:
export DB_USER=postgres
export DB_PASSWORD=<your db password>
export SUPA_PROJECT_REF=<your Supabase project ref>
export DB_URL=postgresql://$DB_USER:$DB_PASSWORD@db.$SUPA_PROJECT_REF.supabase.co:5432/postgres

# Run FerretDB in docker container
docker run -p 27017:27017 -p 8080:8080 -e FERRETDB_POSTGRESQL_URL=$DB_URL ghcr.io/ferretdb/ferretdb

# Consturct mongodb url
export MONGODB_URL=mongodb://$DB_USER:$DB_PASSWORD@127.0.0.1:27017/ferretdb?authMechanism=PLAIN

# Test with mongosh
# If you have mongodb installed locally
mongosh '$MONGODB_URL'
# If you don't have mongodb installed locally, run in docker container
docker run --rm -it --network=ferretdb --entrypoint=mongosh mongo \
 "$MONGODB_URL"

# Test with MongoClient in TypeScript
deno run -A mongo.ts
```

## Deploy to Fly.io

- fly launch --no-deploy
  - An existing fly.toml file was found for app supa-ferretdb
  - ? Would you like to copy its configuration to the new app? (y/N) > y
- fly secrets set FERRETDB_POSTGRESQL_URL=$DB_URL
- fly deploy
- fly ips allocate-v4
  - Note: this is a paid feature! You can test it for free as long as you [release the dedicated IPv4](https://community.fly.io/t/we-are-going-to-start-charging-for-dedicated-ipv4-in-january-1st/15970#how-not-to-be-billed-2) before the end of the billing period!

Now simply replace `127.0.0.1` in the `MONGODB_URL` with your dedicated IPv4 address and you're ready to roll \o/
