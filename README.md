# 🏭 ng-graphql-playground

A high-performance, type-safe Full Stack Monorepo designed to manage long-running manufacturing workflows (`Build`, `Parts`, `Test Run`).

This project uses a hybrid data engine to achieve both maximum developer velocity for real-time dashboards and bare-metal ingestion speeds for automated factory machinery.

---

## 🏗️ Architecture Blueprint

```text
        ┌──────────────────────────────────────────────┐
        │         Angular UI (Apollo / Urql)           │
        └──────────────────────┬───────────────────────┘
                               │ ▲
  GraphQL Queries & Mutations  │ │  Real-time Subscriptions
                               ▼ │  (WebSockets / SSE)
         ┌──────────────────────────────────────────────┐
         │        Hot Chocolate GraphQL Gateway         │
         └──────────────────────┬───────────────────────┘
                                │
          Enforces Projections, │ Dispatches High-Frequency
          Filters & DataLoaders │ Telemetry / Dense Audits
                                ▼
         ┌──────────────────────────────────────────────┐
         │           ASP.NET Core Back-End              │
         └──────────────┬────────────────┬──────────────┘
                        │                │
Domain Orchestration /  │                │ High-Velocity Direct SQL
Elsa State Automation   │                │ Execution (Bypass Change Tracker)
                        ▼                ▼
         ┌──────────────────────┐┌──────────────────────┐
         │  Entity Framework    ││                      │
         │      Core Context    ││    Dapper Engine     │
         └──────────────┬───────┘└───────┬──────────────┘
                        │                │
                 Native │                │ Shares Connection &
            ORM Queries │                │ Local ADO.NET Transaction
                        ▼                ▼
         ┌──────────────────────────────────────────────┐
         │           Microsoft SQL Server               │
         └──────────────────────────────────────────────┘
```


---

## 📁 Repository Directory Structure

```text
ng-graphql-playground/
├── /backend                    # .NET 8/9 ASP.NET Core Solution
│   ├── /src
│   │   ├── /FactoryApp.WebApi  # Web API Entry, Hot Chocolate Setup, Elsa 3 Runtime
│   │   │   ├── schema.graphql  # Auto-emitted GraphQL schema file on local build
│   │   ├── /FactoryApp.Domain  # Core entities (Build, Part, TestRun) & Data Context
│   │   ├── /FactoryApp.GraphQL # Hot Chocolate GraphQL Query, Mutation, & DataLoaders
│   │   └── /FactoryApp.Workflows # Elsa 3 Activities & High-Speed Dapper SQL scripts
├── /frontend                   # Angular Workspace
│   ├── /src
│   │   └── /app
│   │       ├── /graphql        # Front-end UI operations definition (.graphql files)
│   │       ├── /api/generated  # Target for GraphQL Code-Gen (Outputs: graphql.ts)
│   │       └── /components     # Smart/Dumb Angular component layouts (OnPush enabled)
│   ├── codegen.ts              # Automatically maps schema.graphql to type-safe TypeScript
│   └── package.json            # Front-end scripts and web dependencies
└── README.md
```

---

## ⚙️ Core Technical Rules

### 1. The Separation of Concerns

* **EF Core** handles all system reads (GraphQL), schema migrations, and **Elsa 3 Workflow Engine** operations. The context defaults to `NoTracking` to match Dapper's read performance.
* **Dapper** is used strictly for high-frequency writes (automated testing metrics, high-speed sorting arrays) to bypass all ORM state overhead.

### 2. The Shared Transaction Rule

When a mutation updates a core asset state via EF Core while logging bulk metrics via Dapper, they 
**must** share an explicit ADO.NET transaction context to prevent deadlocks:

```csharp
using var transaction = await context.Database.BeginTransactionAsync();
var dbConnection = context.Database.GetDbConnection();
var dbTransaction = context.Database.CurrentTransaction?.GetDbTransaction();

// Execute Dapper code passing `transaction: dbTransaction` here
// Execute EF Core SaveChangesAsync() here
await transaction.CommitAsync();
```

### 3. Automated Type Safety Pipeline

Type safety is fully automated during a local build. Changing a backend C# DTO or schema updates the pipeline automatically:

1. Compile backend code (`dotnet build`).
2. MSBuild exports the unified `schema.graphql` to disk.
3. The frontend file-watcher triggers **GraphQL Code Generator**.
4. Type-safe Angular services (`graphql.ts`) update automatically.

---

## 🚀 Quickstart Local Environment

### Prerequisites

* [.NET SDK](https://microsoft.com) (Version 8.0 or later)
* [Node.js](https://nodejs.org) (Version 18 or later)
* [pnpm](https://pnpm.io/) (Version 8.0 or later) — Package manager for monorepo
* [Docker Desktop](https://www.docker.com/products/docker-desktop) (for SQL Server 2022)

### One-Time Setup

```bash
pnpm install
pnpm setup
# Starts SQL Server container and runs database migrations
```

### Daily Development

**Terminal 1: Backend (.NET with hot-reload)**
```bash
pnpm dev:backend
# Runs: dotnet watch run on Port 5000
```

**Terminal 2: Frontend (Angular with HMR)**
```bash
pnpm dev:frontend
# Runs: pnpm --filter frontend run ng serve on Port 4200
```

**Or run both concurrently:**
```bash
pnpm dev
# Starts both backend and frontend watchers simultaneously
```

**Open browser to:** `http://localhost:4200`

### Access Services

| Service | URL |
|---------|-----|
| **GraphQL Endpoint** | `http://localhost:5000/graphql` |
| **Angular Frontend** | `http://localhost:4200` |
| **SQL Server** | `Server=localhost,1433; User Id=sa; Password=P@ssw0rd1234!` |

### Stop Services

```bash
pnpm docker:down
```

### Full Docker Command Reference

```bash
pnpm docker:up          # Start SQL Server container
pnpm docker:down        # Stop SQL Server container
pnpm docker:clean       # Remove all containers and data volumes
pnpm docker:logs        # View SQL Server logs
pnpm db:migrate         # Run EF Core migrations
```

For comprehensive setup instructions, see [**docs/SETUP.md**](./docs/SETUP.md)
