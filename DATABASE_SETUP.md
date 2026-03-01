# Hospital Dashboard - Database Setup Guide

## Overview

This application now uses a **PostgreSQL database** with **Prisma ORM** to store and manage real hospital data. All mock data has been replaced with actual database-driven functionality.

## Database Architecture

### Tables

1. **departments** - Hospital departments (Emergency, ICU, Cardiology, etc.)
2. **patients** - Patient records with demographics and contact info
3. **admissions** - Patient admission records and status
4. **appointments** - Patient appointments and schedules
5. **beds** - Hospital beds with occupancy status
6. **resources** - Department resources (equipment, supplies)
7. **revenue** - Monthly revenue tracking by department

## Setup Instructions

### 1. Install Dependencies

If you haven't already, install the required npm packages:

```bash
npm install
```

Make sure `@prisma/client` is installed (should already be in package.json).

### 2. Configure Environment Variables

Copy `.env.example` to `.env.local` and configure:

```bash
cp .env.example .env.local
```

Edit `.env.local` with your PostgreSQL connection string:

```env
DATABASE_URL="postgresql://user:password@localhost:5432/hospital_dashboard?schema=public"
```

**Example for local PostgreSQL:**
```env
DATABASE_URL="postgresql://postgres:password@localhost:5432/hospital_dashboard?schema=public"
```

### 3. Create PostgreSQL Database

```bash
createdb hospital_dashboard
```

Or using `psql`:

```sql
CREATE DATABASE hospital_dashboard;
```

### 4. Run Prisma Migrations

Initialize the database schema:

```bash
npx prisma migrate dev --name init
```

This will:
- Create all tables based on `prisma/schema.prisma`
- Generate Prisma Client

### 5. Seed Sample Data (Optional)

Populate the database with sample data:

```bash
npx prisma db seed
```

This creates:
- 8 departments
- 50 patients
- 20 admissions
- 30 appointments
- Beds for each department
- 12 months of revenue data

## Data Management

### Admin Pages

Access the admin panel to manage data:

- **Patients**: `/admin/patients` - Add and view patient records
- **Admissions**: `/admin/admissions` - Create and manage admissions
- **Appointments**: `/admin/appointments` - Schedule appointments
- **Departments**: `/admin/departments` - Manage departments

### API Endpoints

#### Patients
- `GET /api/patients?page=1&limit=50&search=query` - Get patients
- `POST /api/patients` - Create patient

#### Admissions
- `GET /api/admissions?page=1&limit=50&status=Active` - Get admissions
- `POST /api/admissions` - Create admission

#### Appointments
- `GET /api/appointments?page=1&limit=50&status=Scheduled` - Get appointments
- `POST /api/appointments` - Create appointment

#### Departments
- `GET /api/departments` - Get all departments
- `POST /api/departments` - Create department

#### Dashboard Stats
- `GET /api/dashboard/stats` - Get real-time analytics
  - Total patients
  - Active admissions
  - Average wait time
  - Bed occupancy rate
  - Monthly revenue
  - Department load details

## Dashboard Features

The dashboard now shows **real data** calculated from the database:

- **KPI Cards**: Real counts from database (total patients, active admissions)
- **Patient Table**: Displays actual patient records
- **Department Load**: Shows real occupancy rates and wait times
- **Bed Occupancy**: Real-time bed status by department
- **Revenue Analytics**: Actual financial data by department
- **Smart Alerts**: Calculated from real occupancy thresholds

## Data Flow

```
Form Input → API Route → Validation → Database → Dashboard
   ↓           ↓              ↓            ↓         ↓
Patient   /api/patients   Check         Insert    Fetch &
Form      Handler          Exists        Data      Display
```

## Important Notes

### Before Going to Production

1. **Database Backup**: Regular backups are essential
   ```bash
   pg_dump hospital_dashboard > backup.sql
   ```

2. **Environment Variables**: Keep `.env.local` secure and never commit to git

3. **Validation**: All API endpoints have input validation

4. **Error Handling**: All forms include error handling with user-friendly messages

### Useful Commands

```bash
# View database in Prisma Studio
npx prisma studio

# Create a new migration
npx prisma migrate dev --name migration_name

# Reset database (deletes all data)
npx prisma migrate reset

# Generate Prisma Client after schema changes
npx prisma generate

# Format Prisma schema
npx prisma format
```

## Troubleshooting

### "PrismaClientInitializationError"
- Ensure PostgreSQL is running
- Check DATABASE_URL in `.env.local`
- Run migrations: `npx prisma migrate dev`

### "Relation does not exist"
- Run migrations: `npx prisma migrate dev`
- Reset database: `npx prisma migrate reset`

### "Connection refused"
- PostgreSQL service not running
- On Windows: Start PostgreSQL from Services
- On Mac: `brew services start postgresql`
- On Linux: `sudo systemctl start postgresql`

## Next Steps

1. Replace all mock data references in components (already done for dashboard)
2. Update any remaining hardcoded data calls
3. Test all admin forms
4. Verify API endpoints return real data
5. Monitor database performance with actual load

## Database Performance Tips

- Create indexes for frequently queried fields (already done via Prisma)
- Use pagination for large result sets (implemented in all GET endpoints)
- Cache frequently accessed data (departments, for example)
- Monitor slow queries in PostgreSQL logs

## Support

For more information:
- Prisma Docs: https://www.prisma.io/docs/
- PostgreSQL Docs: https://www.postgresql.org/docs/
- Next.js API Routes: https://nextjs.org/docs/app/building-your-application/routing/route-handlers
