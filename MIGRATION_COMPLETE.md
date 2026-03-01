# Hospital Dashboard - Complete Migration Guide

## Summary of Changes

Your hospital admin dashboard has been completely transformed from **mock data to a real PostgreSQL database**. Here's what was done:

### ✅ Completed

1. **Prisma Schema** - Created comprehensive data models
   - Departments, Patients, Admissions, Appointments
   - Beds, Resources, Revenue tables
   - Relationships and constraints

2. **API Routes** - Built complete REST API
   - `/api/patients` - Create and fetch patients
   - `/api/admissions` - Manage admissions
   - `/api/appointments` - Schedule appointments
   - `/api/departments` - Manage departments
   - `/api/dashboard/stats` - Real-time analytics

3. **Admin Forms** - Created data entry interfaces
   - Patient registration form
   - Admission creation form
   - Appointment scheduling form
   - Department management

4. **Admin Pages** - Built management interface
   - `/admin/patients` - Patient records
   - `/admin/admissions` - Admission management
   - `/admin/appointments` - Appointment scheduling
   - `/admin/departments` - Department management
   - `/admin` - Admin dashboard

5. **Database Functions** - Created utility functions in `lib/db.ts`
   - Dashboard stats calculations
   - Patient queries with search
   - Admission tracking
   - Department stress analysis

6. **Dashboard Updates** - Converted to real data
   - KPI cards now calculate from database
   - Patient table shows real records
   - Department load shows actual occupancy
   - All mock data removed

## Quick Start (5 minutes)

### 1. Install Dependencies
```bash
npm install
```

### 2. Create .env.local
```bash
cp .env.example .env.local
```

### 3. Configure Database
Edit `.env.local` with your PostgreSQL connection:
```env
DATABASE_URL="postgresql://user:password@localhost:5432/hospital_dashboard?schema=public"
```

### 4. Run Migrations
```bash
npm run prisma:migrate
```

### 5. Seed Data (Optional)
```bash
npm run prisma:seed
```

### 6. Start Development Server
```bash
npm run dev
```

Visit: http://localhost:3000

## Project Structure

```
app/
├── admin/                          # Admin management pages
│   ├── page.tsx                    # Admin dashboard
│   ├── patients/page.tsx           # Patient management
│   ├── admissions/page.tsx         # Admission management
│   ├── appointments/page.tsx       # Appointment management
│   └── departments/page.tsx        # Department management
├── api/
│   ├── patients/route.ts           # Patient API
│   ├── admissions/route.ts         # Admission API
│   ├── appointments/route.ts       # Appointment API
│   ├── departments/route.ts        # Department API
│   └── dashboard/route.ts          # Analytics API

components/
├── forms/
│   ├── patient-form.tsx            # Patient entry form
│   ├── admission-form.tsx          # Admission entry form
│   └── appointment-form.tsx        # Appointment entry form
├── dashboard/
│   ├── dashboard-shell.tsx         # Main dashboard (UPDATED)
│   ├── kpi-cards.tsx               # KPI metrics (UPDATED)
│   └── ...other components

lib/
├── db.ts                           # Database utilities (NEW)
├── hospital-data.ts                # Legacy mock data (DEPRECATED)
└── ...

prisma/
├── schema.prisma                   # Database schema (UPDATED)
└── seed.ts                         # Sample data (NEW)
```

## Key Files Modified

### 1. Prisma Schema
**File:** `prisma/schema.prisma`
- Switched from SQLite to PostgreSQL
- Created 7 main tables with relationships
- Added timestamps and indexes

### 2. API Routes (NEW)
- `app/api/patients/route.ts` - CRUD operations
- `app/api/admissions/route.ts` - Admission management
- `app/api/appointments/route.ts` - Appointment scheduling
- `app/api/departments/route.ts` - Department management

### 3. Database Utilities (NEW)
**File:** `lib/db.ts`
- Prisma client singleton
- `getDashboardStats()` - Calculate real metrics
- `getPatients()` - Patient queries with search
- `getAdmissions()` - Admission tracking
- `getDepartmentStress()` - Real occupancy analysis

### 4. Admin Pages (NEW)
- Complete data entry and viewing interfaces
- Real-time updates
- Error handling and validation

### 5. Dashboard Shell (UPDATED)
**File:** `components/dashboard/dashboard-shell.tsx`
- Changed from mock data to real database queries
- Simplified to 4 main sections (Overview, Departments, Patients, Admin)
- Real-time refresh using SWR

## How Data Flows

```
User Input Form → API Route → Validation
           ↓
    Database (Prisma)
           ↓
    Dashboard Query
           ↓
    Real-time Display
```

### Example: Adding a Patient

1. **User fills form** at `/admin/patients`
2. **Submit button** sends POST to `/api/patients`
3. **API validates** the data
4. **Inserts into** database via Prisma
5. **Dashboard** automatically updates via SWR polling
6. **Real metrics** recalculated from database

## API Examples

### Create Patient
```javascript
fetch('/api/patients', {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify({
    mrn: 'MRN-123456',
    firstName: 'John',
    lastName: 'Doe',
    age: 45,
    gender: 'Male',
    email: 'john@example.com',
    departmentId: 'dept-123'
  })
})
```

### Get Dashboard Stats
```javascript
fetch('/api/dashboard/stats')
  .then(r => r.json())
  .then(data => {
    console.log(data.totalPatients)
    console.log(data.bedOccupancyRate)
    console.log(data.departmentLoad)
  })
```

### Search Patients
```javascript
fetch('/api/patients?search=john&page=1&limit=50')
```

## Database Commands

```bash
# View database in Prisma Studio
npm run prisma:studio

# Create a migration
npm run prisma:migrate

# Reset entire database
npm run db:reset

# Seed sample data
npm run prisma:seed

# View Prisma schema
npx prisma format
```

## Forms Overview

### Patient Form (`/admin/patients`)
- MRN (Medical Record Number)
- Demographics (Name, Age, Gender, Blood Type)
- Contact Information
- Medical History (Allergies, Conditions)
- Insurance Information

### Admission Form (`/admin/admissions`)
- Select Patient & Department
- Admission Date & Priority
- Wait Time & Status
- Reason & Notes

### Appointment Form (`/admin/appointments`)
- Select Patient & Department
- Date, Time, Duration
- Type (Consultation, Follow-up, Procedure, Emergency)
- Status & Notes

### Department Form (`/admin/departments`)
- Department Name
- Bed Capacity
- Staff Count

## Dashboard Metrics

### KPI Cards (Real Data)
- **Total Patients** - COUNT(*) from patients table
- **Active Admissions** - COUNT(*) where status='Active'
- **Avg Wait Time** - AVG(waitTime) from active admissions
- **Bed Occupancy** - Occupied beds / Total beds
- **Monthly Revenue** - SUM(amount) from revenue table
- **Upcoming Appointments** - COUNT(*) for next 7 days

### Department Load
Each department shows:
- Current occupancy rate
- Current load vs capacity
- Average wait time
- Staff count
- Risk level (calculated from occupancy)

## Validation & Error Handling

All API routes include:
- ✅ Required field validation
- ✅ Data type checking
- ✅ Duplicate detection
- ✅ Relationship verification
- ✅ Error messages
- ✅ HTTP status codes

### Example Error Handling
```javascript
if (!body.mrn || !body.firstName || !body.lastName) {
  return NextResponse.json(
    { error: "Missing required fields" },
    { status: 400 }
  )
}
```

## Migration Checklist

- [ ] PostgreSQL installed and running
- [ ] `.env.local` configured
- [ ] `npm install` completed
- [ ] `npm run prisma:migrate` executed
- [ ] `npm run prisma:seed` executed (optional)
- [ ] `npm run dev` started
- [ ] Forms tested at `/admin/patients`
- [ ] Dashboard shows real data
- [ ] API endpoints returning data

## Troubleshooting

### "PrismaClientInitializationError"
```bash
# Ensure migrations are run
npm run prisma:migrate

# Check DATABASE_URL in .env.local
# PostgreSQL must be running
```

### "Database does not exist"
```bash
# Create database
createdb hospital_dashboard

# Run migrations
npm run prisma:migrate
```

### "Port 3000 in use"
```bash
# Use different port
npm run dev -- -p 3001
```

## What Was Removed

- ❌ All hardcoded mock data in `lib/hospital-data.ts`
- ❌ Simulated patient generation
- ❌ Random analytics calculations
- ❌ AI/ML simulation engines
- ✅ All replaced with real database queries

## What's New

- ✅ PostgreSQL database
- ✅ 7 production-ready tables
- ✅ Complete API layer
- ✅ Admin management pages
- ✅ Data entry forms
- ✅ Real-time dashboard
- ✅ Input validation
- ✅ Error handling

## Next Steps

1. **Populate database** - Use admin forms to add real data
2. **Test all forms** - Verify data entry and validation
3. **Monitor API** - Check request/response in browser DevTools
4. **Backup database** - Regular PostgreSQL backups
5. **Scale up** - Add more departments, beds, and resources

## Performance Optimization

- ✅ Pagination on all GET endpoints
- ✅ Database indexes on foreign keys
- ✅ Search optimization with proper WHERE clauses
- ✅ Efficient JOIN queries
- ✅ Connection pooling via Prisma

## Security Notes

- Store `.env.local` safely (never commit to git)
- Use strong PostgreSQL passwords in production
- Add authentication to admin routes in production
- Implement rate limiting on API endpoints
- Validate all user inputs

## Support Resources

- **Prisma Docs:** https://www.prisma.io/docs/
- **PostgreSQL Docs:** https://www.postgresql.org/docs/
- **Next.js API Routes:** https://nextjs.org/docs/app/building-your-application/routing/route-handlers

## Success Indicators

Your migration is complete when:
1. ✅ Forms insert data without errors
2. ✅ Dashboard displays real patient counts
3. ✅ Department occupancy rates update
4. ✅ API returns real data
5. ✅ No hardcoded mock data visible

---

**Dashboard Ready!** All mock data has been replaced with a real PostgreSQL database. Start entering data through the admin panel!
