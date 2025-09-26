# 🔄 Complete Action Flow Diagram

This diagram explains the complete nx-migrate-action process from setup to completion.

## 📊 Flow Chart

```mermaid
flowchart TD
    A[🚀 Start] --> B[⚙️ Setup Node.js]
    B --> C[📦 Install deps]
    C --> D[🔍 Check version]
    D --> E{Update needed?}

    E -->|No| F[ℹ️ Up to date]
    F --> G[✅ Complete]

    E -->|Yes| H[📈 nx migrate]
    H --> I[🔧 Install deps]
    I --> J{migrations.json?}

    J -->|No| K[ℹ️ No migrations]
    K --> L[📝 Commit updates]
    L --> M[⚙️ Setup Git]
    M --> N[Continue to validation]

    J -->|Yes| O[📋 Show migrations]
    O --> P[🔧 Run migrations]
    P --> Q{Success?}

    Q -->|No| R[❌ Failed]
    R --> S[💥 Exit]

    Q -->|Yes| T[📝 Commit changes]
    T --> U{push-migrations-json?}

    U -->|yes| V[📝 Add file]
    V --> W[💾 Commit audit]
    W --> X[📁 Keep file]
    X --> N

    U -->|false| Y[🧹 Remove file]
    Y --> Z[🗑️ Clean up]
    Z --> N

    N --> AA{skip-validation?}

    AA -->|true| AB[⏭️ Skip tests]
    AB --> AC{auto-merge?}

    AA -->|false| AD[🔍 Run tests]
    AD --> AE[🎯 nx run-many]
    AE --> AF{Pass?}

    AF -->|Yes| AC
    AF -->|No| AG[❌ Failed]
    AG --> AH{create-pr?}

    AH -->|true| AI[📝 Create PR]
    AH -->|false| AJ[✅ No PR]

    AC -->|true| AK[🚀 Push direct]
    AC -->|false| AI

    AK --> AL[✅ Auto-merged]
    AI --> AM[✅ PR Created]

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

## 📋 Detailed Step Descriptions

### Setup Phase
- **🚀 Start**: Action begins execution
- **⚙️ Setup Node.js**: Configure Node.js version and package manager cache
- **📦 Install deps**: Install project dependencies using configured package manager

### Version Check Phase
- **🔍 Check version**: Compare current Nx package version with latest/specified tag
- **Update needed?**: Determine if an update is available

### Migration Phase (if update needed)
- **📈 nx migrate**: Run `nx migrate [version-tag]` to update package.json and generate migrations
- **🔧 Install deps**: Install updated dependencies after package.json changes
- **migrations.json?**: Check if migration file was created by Nx

### Migration Execution (if migrations.json exists)
- **📋 Show migrations**: Display migration details found in migrations.json
- **🔧 Run migrations**: Execute `nx migrate --run-migrations` to apply code changes
- **Success?**: Verify migrations completed without errors

### Git Operations
- **📝 Commit updates/changes**: Commit package.json updates or all migration changes
- **⚙️ Setup Git**: Configure git user for automated commits
- **push-migrations-json?**: Check configuration for migration file handling
- **📝 Add file / 💾 Commit audit**: Keep migrations.json in repository for audit trail
- **🧹 Remove file / 🗑️ Clean up**: Remove migrations.json locally after successful migration

### Validation Phase
- **skip-validation?**: Check if validation should be bypassed
- **🔍 Run tests**: Execute configured validation commands
- **🎯 nx run-many**: Run `nx run-many --target=build,test --affected/all`
- **Pass?**: Verify all validation commands succeeded

### Branching Strategy
- **auto-merge?**: Check if automatic merging is enabled
- **create-pr?**: Check if PR should be created on validation failure
- **🚀 Push direct**: Push changes directly to target branch
- **📝 Create PR**: Create pull request with detailed information

### Completion
- **✅ Complete**: Action finished - no changes needed
- **✅ Auto-merged**: Changes pushed directly to target branch
- **✅ PR Created**: Pull request created for review
- **✅ No PR**: Validation failed but no PR creation requested
- **💥 Exit**: Action failed due to migration errors

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