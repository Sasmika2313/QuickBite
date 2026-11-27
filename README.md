# QuickBite — Common Foundation

## Included
- ASP.NET Core Web API project targeting .NET 8
- Six shared EF Core entities
- Shared `AppDbContext` and relationship configuration
- SQL Server, Swagger, CORS and JWT bearer authentication registration
- Angular shared configuration snippets
- Git ignore rules
- Setup and ownership instructions

## Important ownership rules
The integration lead owns these shared files:
- `QuickBite.Api/Program.cs`
- `QuickBite.Api/Data/AppDbContext.cs`
- `QuickBite.Api/Models/*`
- `QuickBite.Api/Migrations/*`
- `quickbite-ui-shared/app.config.ts`
- `quickbite-ui-shared/app.routes.ts`

Vertical developers should create their own controllers, DTOs, services, feature components and feature route files in their assigned folders. Do not independently edit shared files.

## Requirements
- .NET 8 SDK
- SQL Server or SQL Server LocalDB
- EF Core CLI (`dotnet tool install --global dotnet-ef --version 8.0.0`)
- Node.js and Angular CLI for the frontend

## Backend setup
1. Open a terminal in `QuickBite.Api`.
2. Restore packages:
   ```bash
   dotnet restore
   ```
3. Configure a local SQL Server connection string and JWT key. Prefer user-secrets:
   ```bash
   dotnet user-secrets init
   dotnet user-secrets set "ConnectionStrings:DefaultConnection" "Server=localhost;Database=QuickBiteDB;Trusted_Connection=True;TrustServerCertificate=True;"
   dotnet user-secrets set "Jwt:Key" "REPLACE_WITH_A_RANDOM_SECRET_AT_LEAST_32_CHARACTERS"
   ```
4. Add the initial migration and create the database:
   ```bash
   dotnet ef migrations add InitialCreate
   dotnet ef database update
   ```
5. Run:
   ```bash
   dotnet run
   ```
6. Open the Swagger URL printed by `dotnet run`.

If your SQL Server instance is named (for example `localhost\\SQLEXPRESS`), use that exact server name in the connection string.

## Angular setup
The `quickbite-ui-shared` directory contains shared files to copy into an Angular standalone application created by the integration lead. It is intentionally not a second Angular project. Keep the same Angular version and package lock file across the team.

Create the Angular project once:
```bash
ng new quickbite-ui --standalone --routing --style=css
```
Copy the files from `quickbite-ui-shared/src/app/` into the generated `src/app/` and copy `environment.ts` into `src/environments/` (create the directory if needed). Adjust the API URL to match the backend HTTPS URL.

## API URL
Update `src/environments/environment.ts` to the HTTPS URL shown by the ASP.NET Core application, followed by `/api`.

## Security notes
- No real JWT secret is included in this ZIP.
- Do not commit local secrets or `appsettings.Development.json`.
- The API validates JWTs, but individual endpoints still need `[Authorize]` or `[Authorize(Roles = "...")]`.
- Registration must never accept an Admin role from an untrusted client. Assign `Customer` on public registration; seed or provision admin accounts separately.
- Checkout must calculate prices and user identity on the server, not trust browser-supplied values.
