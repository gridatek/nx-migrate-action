# 🔄 Complete Action Flow Diagram

This diagram explains the complete nx-migrate-action process from setup to completion.

## 📊 Flow Chart

```mermaid
flowchart TD
    A[🚀 Action Start] --> B[⚙️ Setup Node.js & Cache]
    B --> C[📦 Install Dependencies]
    C --> D[🔍 Check Current vs Latest Nx Version]
    D --> E{Update needed?}

    E -->|No| F[ℹ️ Already up to date]
    F --> G[✅ Action Complete - No Changes]

    E -->|Yes| H[📈 Run nx migrate]
    H --> I[🔧 Install dependencies after migration]
    I --> J{migrations.json created?}

    J -->|No| K[ℹ️ No migrations needed]
    K --> L[📝 Commit package updates]
    L --> M[⚙️ Setup Git user]
    M --> N[✅ Continue to validation]

    J -->|Yes| O[📋 Display migration details]
    O --> P[🔧 Run nx migrate --run-migrations]
    P --> Q{Migration successful?}

    Q -->|No| R[❌ Migration Failed]
    R --> S[💥 Action Fails]

    Q -->|Yes| T[📝 Commit all changes]
    T --> U{push-migrations-json = yes?}

    U -->|Yes| V[📝 git add migrations.json]
    V --> W[💾 git commit 'add migrations.json for audit trail']
    W --> X[📁 Keep migrations.json in repo]
    X --> N

    U -->|No| Y[🧹 rm migrations.json]
    Y --> Z[🗑️ Remove file locally]
    Z --> N

    N --> AA{skip-validation = true?}

    AA -->|Yes| AB[⏭️ Skip validation]
    AB --> AC{auto-merge-on-success = true?}

    AA -->|No| AD[🔍 Run validation commands]
    AD --> AE[🎯 nx run-many --target=build,test]
    AE --> AF{Validation passes?}

    AF -->|Yes| AC
    AF -->|No| AG[❌ Validation Failed]
    AG --> AH{create-pr-on-failure = true?}

    AH -->|Yes| AI[📝 Create Pull Request]
    AH -->|No| AJ[✅ Done - No PR Created]

    AC -->|Yes| AK[🚀 Push to target branch]
    AC -->|No| AI

    AK --> AL[✅ Done - Auto-merged]
    AI --> AM[✅ Done - PR Created]

    style A fill:#e1f5fe
    style G fill:#c8e6c9
    style AL fill:#c8e6c9
    style AM fill:#c8e6c9
    style AJ fill:#c8e6c9
    style S fill:#ffcdd2
    style R fill:#ffcdd2
    style AG fill:#ffcdd2
    style X fill:#fff3e0
    style Z fill:#f3e5f5
```

## 🎯 Key Decision Points

### 1. **Update Check**
- **Update Available**: Proceeds with migration process
- **Already Up-to-date**: Action completes successfully with no changes

### 2. **migrations.json Creation**
- **Created**: Nx found changes requiring migrations
- **Not Created**: No migrations needed, commits package updates only

### 3. **Migration Execution**
- **Success**: Migrations applied successfully, continues to validation
- **Failure**: Action stops and reports error

### 4. **push-migrations-json Option**
- **`yes`**: Commits migrations.json to repository for audit trail
- **`false`** (default): Removes migrations.json locally after success

### 5. **Validation Control**
- **skip-validation = true**: Bypasses all validation, goes to branching logic
- **skip-validation = false**: Runs configured validation commands

### 6. **Branching Strategy**
- **Auto-merge**: Direct push to target branch if validation passes and enabled
- **PR Creation**: Creates PR if validation fails or auto-merge disabled

## 📝 Example Scenarios

### Scenario A: Complete Success with Auto-merge
```
Start → Setup → Install → Version Check → Migrate → Run Migrations → Validate → Auto-merge ✅
```

### Scenario B: Validation Failure with PR Creation
```
Start → Setup → Install → Version Check → Migrate → Run Migrations → Validate ❌ → Create PR
```

### Scenario C: No Update Needed
```
Start → Setup → Install → Version Check → Already up-to-date ✅
```

### Scenario D: Skip Validation with PR
```
Start → Setup → Install → Version Check → Migrate → Skip Validation → Create PR
```

### Scenario E: Migration File Audit Trail
```
Start → Setup → Install → Version Check → Migrate → Commit migrations.json → Validate → Auto-merge/PR
```

### Scenario F: Migration Execution Failure
```
Start → Setup → Install → Version Check → Migrate → Migration fails ❌ → Action fails
```

## 🔧 Configuration Impact

| Setting | Result |
|---------|--------|
| `nx-version-tag: latest` | Uses stable release version |
| `nx-version-tag: canary/next` | Uses pre-release version |
| `push-migrations-json: yes` | migrations.json preserved in Git history |
| `push-migrations-json: false` | migrations.json removed after successful migration |
| `auto-merge-on-success: true` | Direct push to target branch when validation passes |
| `auto-merge-on-success: false` | Always create PR for review |
| `skip-validation: true` | Skip validation commands, proceed to branching logic |
| `skip-validation: false` | Run validation commands before branching |
| `create-pr-on-failure: true` | Create PR when validation fails |
| `create-pr-on-failure: false` | No PR creation on validation failure |
| `validation-scope: affected` | Only validate affected projects |
| `validation-scope: all` | Validate all projects in workspace |

## 🎨 Legend

- 🚀 **Start/Action**: Process initiation
- 📦 **Process**: Core operations
- ❓ **Decision**: Conditional logic points
- 📁 **Storage**: File persistence
- 🗑️ **Cleanup**: File removal
- ✅ **Success**: Successful completion
- ❌ **Failure**: Error states