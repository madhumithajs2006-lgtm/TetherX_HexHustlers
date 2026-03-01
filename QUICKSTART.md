# Hospital Dashboard - Quick Start & Troubleshooting

## ⚡ Quick Start (5 Minutes)

### Step 1: Prepare Environment
```bash
# Copy environment template
cp .env.example .env.local

# Edit .env.local and add your database URL
# For local PostgreSQL:
# DATABASE_URL="postgresql://postgres:password@localhost:5432/hospital_dashboard?schema=public"
```

### Step 2: Install & Setup Database
```bash
# Install npm dependencies
npm install

# Run database migrations
npm run prisma:migrate

# Seed with sample data (optional)
npm run prisma:seed
```

### Step 3: Start Development Server
```bash
npm run dev
```

### Step 4: Access Dashboard
- Open http://localhost:3000/dashboard
- Go to http://localhost:3000/admin to manage data

---

## 🐛 Troubleshooting Guide

### Issue: "PrismaClientInitializationError: error while loading the database driver"

**Cause:** Database connection problem or Prisma migration not run

**Solutions:**
```bash
# 1. Ensure PostgreSQL is running
# macOS: brew services start postgresql
# Windows: Start PostgreSQL from Services
# Linux: sudo systemctl start postgresql

# 2. Run migrations
npm run prisma:migrate

# 3. Check DATABASE_URL in .env.local
# Format: postgresql://user:password@host:port/database?schema=public
```

---

### Issue: "error: database "hospital_dashboard" does not exist"

**Cause:** PostgreSQL database not created

**Solutions:**
```bash
# Option 1: Create database using psql
createdb hospital_dashboard

# Option 2: Create manually in SQL
# Log into PostgreSQL:
psql -U postgres

# Then run:
# CREATE DATABASE hospital_dashboard;
# \q

# Then run migrations:
npm run prisma:migrate
```

---

### Issue: "Unable to connect to database"

**Cause:** PostgreSQL not running or wrong credentials

**Solutions:**
```bash
# 1. Verify PostgreSQL is running
# Windows: Check Services (psql)
# macOS: brew services list | grep postgres
# Linux: sudo systemctl status postgresql

# 2. Test connection manually
psql -U postgres -h localhost

# 3. Verify credentials in .env.local
# Default PostgreSQL user: postgres
# Most installations require password

# 4. Restart PostgreSQL
# macOS: brew services restart postgresql
# Windows: Restart PostgreSQL service
# Linux: sudo systemctl restart postgresql
```

---

### Issue: "EACCES: permission denied" when running npm commands

**Cause:** npm permissions issue

**Solutions:**
```bash
# Option 1: Use sudo (not recommended)
sudo npm run prisma:migrate

# Option 2: Fix npm permissions
mkdir ~/.npm-global
npm config set prefix '~/.npm-global'
export PATH=~/.npm-global/bin:$PATH

# Option 3: Use nvm (recommended)
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/master/install.sh | bash
nvm install 18
nvm use 18
npm run prisma:migrate
```

---

### Issue: "Error: ENOENT: no such file or directory, open '.env.local'"

**Cause:** .env.local file not created

**Solutions:**
```bash
# Create .env.local from template
cp .env.example .env.local

# Edit the file with your database URL
nano .env.local  # or use your editor
```

---

### Issue: Admin forms not working / "POST /api/patients 500"

**Cause:** API route error

**Solutions:**
```bash
# 1. Check browser console (F12) for errors
# 2. Check terminal for error logs
# 3. Verify all required fields filled
# 4. Ensure database connection is active
# 5. Check DATABASE_URL is set in .env.local

# 6. Verify Prisma generated Client
npx prisma generate

# 7. Check API route files exist
ls -la app/api/patients/route.ts
```

---

### Issue: Dashboard shows "0" for all metrics

**Cause:** No data in database

**Solutions:**
```bash
# 1. Seed sample data
npm run prisma:seed

# 2. Or manually add data using admin forms
# Go to http://localhost:3000/admin/patients
# Fill form and submit

# 3. Check database has data
npx prisma studio
```

---

### Issue: "Cannot find module '@prisma/client'"

**Cause:** Dependencies not installed

**Solutions:**
```bash
# Reinstall all dependencies
rm -rf node_modules package-lock.json
npm install

# Generate Prisma Client
npx prisma generate
```

---

### Issue: "Error: Port 3000 is already in use"

**Cause:** Another application using port 3000

**Solutions:**
```bash
# Option 1: Use different port
npm run dev -- -p 3001
# Access at http://localhost:3001

# Option 2: Kill process using port 3000
# macOS/Linux: lsof -ti:3000 | xargs kill -9
# Windows: netstat -ano | findstr :3000
#   taskkill /PID <PID> /F

# Option 3: Stop other services
# Check if another dev server is running
# Stop Docker containers if any
```

---

### Issue: Forms accept data but dashboard doesn't update

**Cause:** SWR polling issue or API not returning data

**Solutions:**
```bash
# 1. Manually refresh dashboard
# Press F5 or click refresh button

# 2. Check API endpoint directly
# Open browser: http://localhost:3000/api/dashboard/stats
# Should see JSON data

# 3. Check browser console for network errors
# F12 → Network tab → check /api/dashboard/stats request

# 4. Verify database query works
npx prisma studio
# Navigate to each table and check data exists
```

---

### Issue: "Unexpected token < in JSON" in console

**Cause:** API route returning HTML instead of JSON

**Solutions:**
```bash
# 1. Verify route file exists
ls -la app/api/patients/route.ts

# 2. Check route syntax
# Route must export async GET/POST functions

# 3. Restart development server
# Ctrl+C to stop
npm run dev

# 4. Check for syntax errors
npx tsc --noEmit
```

---

### Issue: Database migration shows "already exists" error

**Cause:** Running migrations twice or manual changes

**Solutions:**
```bash
# Option 1: Skip existing migration
npm run prisma:migrate -- --skip-generate

# Option 2: Reset database (WARNING: deletes all data!)
npm run db:reset

# Option 3: View migration status
npx prisma migrate status
```

---

### Issue: TypeScript compilation errors

**Cause:** Type mismatches in code

**Solutions:**
```bash
# 1. Type check without building
npx tsc --noEmit

# 2. Generate Prisma types
npx prisma generate

# 3. Check imports are correct
# Verify paths in tsconfig.json

# 4. Restart IDE for type checking to refresh
```

---

### Issue: Admin pages show blank/loading forever

**Cause:** API not responding

**Solutions:**
```bash
# 1. Check API is working
curl http://localhost:3000/api/departments

# 2. Check terminal for errors
# Look for stack traces

# 3. Verify database connection
npm run prisma:studio
# If studio loads, DB connection works

# 4. Check for infinite loops in components
# Look for missing dependencies in useEffect

# 5. Hard refresh (Ctrl+Shift+R in Chrome)
```

---

## 🔍 Debugging Tips

### View Database with Prisma Studio
```bash
npx prisma studio
```
Access at: http://localhost:5555

### Check Database Connection
```bash
# Using psql
psql -U postgres -d hospital_dashboard -c "SELECT COUNT(*) FROM patients;"

# Using Prisma
npx prisma db execute --stdin < query.sql
```

### View API Responses
```bash
# Using curl
curl http://localhost:3000/api/patients

# Using insomnia/postman
# Set method to GET
# URL: http://localhost:3000/api/patients
```

### Enable Debug Logging
```bash
# Add to .env.local
DEBUG=prisma:*

# Then run development server
npm run dev
```

---

## ✅ Verification Checklist

Run through this if something isn't working:

- [ ] PostgreSQL service is running
- [ ] `.env.local` file exists with correct DATABASE_URL
- [ ] `npm install` completed without errors
- [ ] `npm run prisma:migrate` ran successfully
- [ ] Prisma studio loads: `npx prisma studio`
- [ ] Sample data seeded: `npm run prisma:seed`
- [ ] Dev server running: `npm run dev`
- [ ] Dashboard loads: http://localhost:3000/dashboard
- [ ] Admin page loads: http://localhost:3000/admin
- [ ] Forms visible and functional
- [ ] No red errors in browser console (F12)
- [ ] API returning data: curl http://localhost:3000/api/patients

---

## 📝 Getting Help

### Before Asking for Help, Provide:
1. Full error message (screenshot or copy-paste)
2. Output from terminal where you ran the command
3. Contents of your `.env.local` (without password)
4. Operating system and Node.js version (`node -v`)
5. Steps to reproduce the issue

### Resources:
- **Prisma Docs:** https://www.prisma.io/docs/
- **PostgreSQL Docs:** https://www.postgresql.org/docs/
- **Next.js Docs:** https://nextjs.org/docs/
- **Stack Overflow:** Tag with `prisma` and `next.js`

---

## 🚀 After Setup

Once everything is working:

1. **Add some data** via admin forms
2. **Verify dashboard updates** with real data
3. **Check API responses** in browser
4. **Review schema** in prisma/schema.prisma
5. **Read API_REFERENCE.md** for endpoint details

---

## ⚠️ Important Notes

- Never commit `.env.local` to git
- Database backups are your responsibility
- Test thoroughly before production
- Seed data is for development only
- Reset database deletes ALL data

---

**Still stuck?** Check the other documentation files:
- DATABASE_SETUP.md
- MIGRATION_COMPLETE.md
- IMPLEMENTATION_SUMMARY.md
- API_REFERENCE.md
