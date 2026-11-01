# MarsToolings.Makessoft

A web-based **billing, accounting and inventory management** application built with ASP.NET Core MVC and MySQL. It covers sales and purchase billing (with GST), returns, damage entries, delivery challans, sales orders, cost sheets, receipts and payments, stock statements, export packing lists, and a large set of business and GST reports.

> Built on the Makessoft product codebase and customised for Mars Toolings. The C# namespace is still `Product.Makessoft`.

---

## Features

| Module | What it does |
|---|---|
| **Master** | Customers, suppliers, items, categories, groups / sub-groups, units, areas, banks, ledger heads, narrations, cheque books |
| **Billing** | Sales bill, sales return, sales damage, sales order, purchase bill, purchase return, delivery challan (DC), cost sheet, QR code, monitor |
| **Book Keeping** | Receipts and payments |
| **Stock** | Opening stock, stock statement (with stock value) |
| **Reports** | Receipt / payment outstanding, cash receipt / payment statements, customer, item and receipt reports |
| **Analysis Reports** | Sales / purchase registers (and returns), GSTR reports, sales order report, area-wise outstanding, item-wise / customer-wise reports |
| **Export** | Packing list for export shipments |
| **Utilities** | Settings, visible-field settings, company details |
| **Common** | Login, registration, home dashboard, role-based menus |

## Tech stack

- **Framework:** ASP.NET Core MVC (`netcoreapp2.0`, `Microsoft.AspNetCore.All 2.0.9`)
- **Database:** MySQL via Entity Framework Core (`Pomelo.EntityFrameworkCore.MySql`)
- **Logging:** NLog (file target, `Logs/` folder)
- **Other libraries:** QRCoder, BuildBundlerMinifier
- **Front end:** Razor views, AdminLTE, Bootstrap 4, jQuery, DataTables
- **Pattern:** Controller → Repository (interfaces registered in `Startup.cs`) → `DatabaseContext`

## Repository layout

```
.
├── src/
│   ├── MarsToolings.Makessoft.sln
│   └── MarsToolings.Makessoft/        # the web application
│       ├── Controllers/               # by module (Master, Billing, Reports, ...)
│       ├── Model/                     # entities, view models, DatabaseContext
│       ├── Repository/                # data access (interface + implementation)
│       ├── Views/                     # Razor views and view components
│       ├── Constants/                 # menu data, column definitions, messages
│       ├── Filters/ Helper/ Library/ Extensions/
│       ├── appsettings*.json  NLog.config  bundleconfig.json
├── dbscripts/                         # numbered incremental SQL scripts (00001–00022)
├── MySql/                             # extra table scripts (e.g. Packing)
├── Mars_20201106.sql                  # full schema snapshot
├── dbbackup/                          # database dumps (see Security notes)
├── document/                          # requirement sheets, menu sheet, publishing guide
├── notes/ requirment/                 # developer notes and client requirement notes
```

## Getting started

### Prerequisites

- .NET Core SDK 2.x (the project targets `netcoreapp2.0`)
- MySQL 5.7 / 8.x
- Visual Studio 2017/2019 or the `dotnet` CLI

### Setup

1. **Create the database**
   ```sql
   CREATE DATABASE marstoolings;
   ```
2. **Load the schema** – import `Mars_20201106.sql`, then run any scripts in `dbscripts/` that are newer than your snapshot, in numeric order.
3. **Configure the connection string** in `src/MarsToolings.Makessoft/appsettings.Development.json`:
   ```json
   "ConnectionStrings": {
     "DatabaseConnection": "SERVER=localhost;PORT=3306;UID=<user>;PASSWORD=<password>;DATABASE=marstoolings"
   }
   ```
4. **Run**
   ```bash
   cd src/MarsToolings.Makessoft
   dotnet restore
   dotnet run
   ```
   Default URLs (from `launchSettings.json`): `http://localhost:50582/` (Kestrel) or `http://localhost:50581/` (IIS Express).
5. Open the site – the default route is `Login/Login`. Create a user via the registration page or insert one directly, then sign in.

### Logs

Warnings and errors are written to `Logs/Logs-<date>.log` under the application folder (see `NLog.config`).

## Configuration notes

- Session timeout is 30 minutes (`Startup.cs`).
- Culture is fixed to `en-GB`.
- Menu access is role based: role `1` sees all menus, other roles see the menu IDs stored on their user record.
- Menu entries are seeded from `Constants/MenuMasterData/MenuMasterData.cs` and the `MenuMaster` table.

## Deployment

See `document/Publishing.docx` for the publishing steps used for the hosted environment.

## Security notes — please read before sharing this repository

- `appsettings.json` contains **real database credentials and a server IP address** in plain text. Move secrets to environment variables, user secrets or a secrets manager, and **rotate the passwords that have been committed**.
- `dbbackup/` contains database dumps for client systems. Remove them from the repository (and Git history) before making it public.
- `EncryptionLibrary.cs` uses a hard-coded default key, and passwords are stored with reversible encryption. Replace this with a salted one-way hash (for example ASP.NET Core `PasswordHasher`).
- `src.zip` (about 46 MB) duplicates the source tree and should be removed.

## Known limitations

- The project targets .NET Core 2.0, which is no longer supported. Upgrading to a current LTS version of .NET is recommended.
- The Pomelo EF Core provider version (3.1.0) differs from the EF Core version bundled with `Microsoft.AspNetCore.All 2.0.9`; check package compatibility if restore or build errors appear.
- No automated tests are included.

## License

No license file is present. Add one before distributing the code.

