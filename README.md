# 🗄️ ServiceNow CMDB Governance Framework

> A comprehensive CMDB health improvement framework covering Discovery configuration, CI relationship mapping, CSDM alignment, data normalization, reconciliation policies, and deduplication — built from multi-enterprise governance programs.

<p>
  <img src="https://img.shields.io/badge/ServiceNow-CMDB%20%7C%20CSDM-orange?style=for-the-badge&logo=servicenow&logoColor=white"/>
  <img src="https://img.shields.io/badge/Scope-Enterprise%20Governance-1c2b3a?style=for-the-badge"/>
  <img src="https://img.shields.io/badge/Author-Swarup%20Kumar%20Namana-b8922a?style=for-the-badge"/>
</p>

---

## 📌 Overview

This framework documents the end-to-end approach to building and maintaining a trusted, CSDM-aligned CMDB across large enterprise environments. It covers everything from initial Discovery setup to ongoing governance, data quality monitoring, and CI relationship management.

**Outcomes delivered with this framework:**
- ✅ Trusted infrastructure records supporting change impact analysis
- ✅ CSDM-aligned CI hierarchy enabling business service mapping
- ✅ Automated deduplication reducing duplicate CIs by 90%+
- ✅ Discovery health dashboard with real-time data quality scores
- ✅ Reconciliation rules preventing data overwrites from rogue sources

---

## 📂 Repository Structure

```
servicenow-cmdb-governance/
│
├── 01-foundation/
│   ├── cmdb-strategy.md               # CMDB program strategy & goals
│   ├── csdm-overview.md               # CSDM layers & entity mapping
│   ├── ci-class-selection.md          # Which CI classes to populate
│   └── data-sources-inventory.md      # Mapping all data sources to CI classes
│
├── 02-discovery/
│   ├── discovery-setup.md             # Discovery configuration guide
│   ├── mid-server-placement.md        # MID Server network placement strategy
│   ├── discovery-schedules.md         # Schedule design for large environments
│   ├── pattern-customization.md       # Custom Discovery patterns
│   └── discovery-troubleshooting.md   # Common issues & resolutions
│
├── 03-service-mapping/
│   ├── service-mapping-setup.md       # Service Mapping configuration
│   ├── entry-point-strategy.md        # Entry point selection guide
│   ├── traffic-based-discovery.md     # Traffic-based Service Mapping
│   └── application-service-design.md  # Application Service hierarchy design
│
├── 04-data-quality/
│   ├── reconciliation-rules.md        # Reconciliation rule design
│   ├── deduplication-rules.md         # Deduplication rule patterns
│   ├── identification-rules.md        # CI identification & reclassification
│   └── normalization-rules.md         # Data normalization patterns
│
├── 05-csdm-alignment/
│   ├── csdm-layers.md                 # Foundation → Managed Object layers
│   ├── business-application-mapping.md # Business App → Technical Service
│   ├── logical-service-design.md      # Logical Service hierarchy
│   └── offering-to-service.md        # Service Offering configuration
│
├── 06-governance/
│   ├── cmdb-health-kpis.md            # KPIs & health scoring model
│   ├── data-steward-process.md        # Data stewardship roles & process
│   ├── exception-management.md        # CI exception handling
│   └── continuous-improvement.md      # Ongoing governance process
│
└── 07-reporting/
    ├── health-dashboard.md            # CMDB Health Dashboard setup
    ├── staleness-reports.md           # Stale CI identification & cleanup
    └── coverage-reports.md            # Discovery coverage reporting
```

---

## 🏗️ CSDM Alignment — The Foundation

### CSDM Layers (Top to Bottom)

```
┌─────────────────────────────────────────────────────┐
│                  BUSINESS LAYER                      │
│   Business Capability → Business Application        │
│   Business Service → Service Offering               │
├─────────────────────────────────────────────────────┤
│                  SERVICE LAYER                       │
│   Application Service → Technical Service           │
│   Service Offering → Dynamic CI Group               │
├─────────────────────────────────────────────────────┤
│                 APPLICATION LAYER                    │
│   Application → Application Instance               │
│   Software Product → Software Installation          │
├─────────────────────────────────────────────────────┤
│              INFRASTRUCTURE LAYER                    │
│   Server → Virtual Machine → Container              │
│   Database → Cluster → Storage                      │
├─────────────────────────────────────────────────────┤
│                FOUNDATION LAYER                      │
│   Location → Network → IP Address                   │
│   Hardware → Manufacturer → Model                   │
└─────────────────────────────────────────────────────┘
```

### Entity Relationship: Business App → CI

```
Business Application (Payroll System)
    │
    └── Application Service (Payroll App Service)
            │
            ├── Application Instance (Payroll-PROD)
            │       │
            │       ├── Windows Server (payroll-app01)
            │       ├── Windows Server (payroll-app02)
            │       └── Load Balancer (payroll-lb01)
            │
            └── Application Instance (Payroll-DR)
                    │
                    ├── Windows Server (payroll-dr01)
                    └── Database (payroll-db-dr01)
```

---

## 🔍 Discovery Configuration

### MID Server Placement Strategy

```
Corporate Network
│
├── Subnet 10.0.1.0/24  (Data Center East)
│       └── MID Server: mid-dc-east-01
│               └── Discovers: Servers, Network, Storage
│
├── Subnet 10.0.2.0/24  (Data Center West)
│       └── MID Server: mid-dc-west-01
│               └── Discovers: Servers, Network, Storage
│
├── Subnet 10.0.10.0/24  (Cloud - AWS)
│       └── MID Server: mid-aws-01
│               └── Discovers: EC2, RDS, ELB, S3
│
└── Subnet 10.0.20.0/24  (Cloud - Azure)
        └── MID Server: mid-azure-01
                └── Discovers: VMs, SQL, App Services
```

### Discovery Schedule Design

| Schedule Name | Target Range | Frequency | MID Server |
|--------------|-------------|-----------|------------|
| DC-East-Servers | 10.0.1.0/24 | Daily 2AM | mid-dc-east-01 |
| DC-West-Servers | 10.0.2.0/24 | Daily 3AM | mid-dc-west-01 |
| Network-Devices | 10.0.0.0/16 | Weekly Sun | mid-dc-east-01 |
| AWS-Cloud | AWS Account | Every 4h | mid-aws-01 |
| Azure-Cloud | Azure Sub | Every 4h | mid-azure-01 |
| Full-Sweep | All ranges | Monthly | Round-robin |

### Discovery Credential Best Practices
```
DO:
  ✅ Use dedicated service accounts for Discovery
  ✅ Store credentials in Credential Aliases (never hardcode)
  ✅ Use least-privilege accounts (read-only where possible)
  ✅ Rotate credentials every 90 days
  ✅ Use different accounts per environment (prod/non-prod)

DON'T:
  ❌ Use domain admin accounts for Discovery
  ❌ Store credentials in Discovery schedules directly
  ❌ Share Discovery credentials with other integrations
  ❌ Use personal user accounts for Discovery
```

---

## 🔧 Reconciliation Rules

### What Reconciliation Does
Reconciliation rules determine **which data source wins** when multiple sources report values for the same CI attribute — preventing lower-quality sources from overwriting trusted data.

### Reconciliation Rule Design

```
Priority Order (Highest to Lowest):
1. ServiceNow Discovery (most trusted — agent-based)
2. SCCM / Intune (asset management tools)
3. Manual Entry (human-verified data)
4. Import Sets (bulk imports)
5. Monitoring Tools (least trusted for inventory)
```

### Example: Server Hostname Reconciliation
```
Attribute: host_name (cmdb_ci_server)

Rule: "Discovery Wins on Hostname"
  IF source = ServiceNow Discovery
  THEN accept value
  ELIF source = SCCM AND Discovery has no value
  THEN accept value
  ELIF source = Import Set AND Discovery AND SCCM have no value
  THEN accept value
  ELSE REJECT — keep existing value
```

### Script: Custom Reconciliation Logic
```javascript
// Reconciliation Script for custom attributes
// Runs during IRE (Identification and Reconciliation Engine)

var ReconciliationHelper = Class.create();
ReconciliationHelper.prototype = {
    initialize: function() {},

    // Determine winning value based on source priority
    resolveConflict: function(attribute, values) {
        var sourcePriority = {
            'ServiceNow Discovery': 1,
            'SCCM':                 2,
            'Manual':               3,
            'Import':               4,
            'Monitoring':           5
        };

        var winner = null;
        var highestPriority = 999;

        values.forEach(function(entry) {
            var priority = sourcePriority[entry.source] || 99;
            if (priority < highestPriority && entry.value) {
                highestPriority = priority;
                winner = entry.value;
            }
        });

        return winner;
    },

    type: 'ReconciliationHelper'
};
```

---

## 🔄 Deduplication Rules

### Deduplication Identification Keys

| CI Class | Primary Key Fields | Secondary Key Fields |
|----------|-------------------|---------------------|
| cmdb_ci_server | serial_number | name + ip_address |
| cmdb_ci_win_server | serial_number | name + os_version |
| cmdb_ci_linux_server | serial_number | name + ip_address |
| cmdb_ci_database | name + db_type | host + port |
| cmdb_ci_network_adapter | mac_address | ip_address |
| cmdb_ci_appl | name + version | vendor + install_path |

### Duplicate Identification Script

```javascript
// Scheduled Script: Run daily to find and flag duplicates
// Table: cmdb_ci_server

var DuplicateFinder = Class.create();
DuplicateFinder.prototype = {
    initialize: function() {
        this.duplicateCount = 0;
    },

    findDuplicates: function() {
        // Group by serial number — find any with more than 1 record
        var ga = new GlideAggregate('cmdb_ci_server');
        ga.addNotNullQuery('serial_number');
        ga.addQuery('install_status', '!=', '7'); // Exclude retired
        ga.groupBy('serial_number');
        ga.addAggregate('COUNT');
        ga.addHaving('COUNT', '>', 1);
        ga.query();

        while (ga.next()) {
            var serialNum = ga.serial_number.toString();
            var count = ga.getAggregate('COUNT');

            gs.info('Duplicate found: Serial=' + serialNum + ' Count=' + count);
            this._flagDuplicates(serialNum);
            this.duplicateCount++;
        }

        gs.info('Total duplicate groups found: ' + this.duplicateCount);
        return this.duplicateCount;
    },

    _flagDuplicates: function(serialNumber) {
        // Get all CIs with this serial number, sorted by last_discovered
        var gr = new GlideRecord('cmdb_ci_server');
        gr.addQuery('serial_number', serialNumber);
        gr.orderByDesc('last_discovered');
        gr.query();

        var isFirst = true;
        while (gr.next()) {
            if (isFirst) {
                // Keep the most recently discovered — mark as authoritative
                gr.u_duplicate_status = 'authoritative';
                isFirst = false;
            } else {
                // Mark others as duplicates for review
                gr.u_duplicate_status = 'duplicate';
                gr.u_duplicate_of = gr.sys_id; // Will be set properly
            }
            gr.update();
        }
    },

    type: 'DuplicateFinder'
};

// Execute
var finder = new DuplicateFinder();
finder.findDuplicates();
```

---

## 📊 CMDB Health KPIs

### Health Score Model (0–100)

| Dimension | Weight | Measurement |
|-----------|--------|-------------|
| Completeness | 25% | % of mandatory attributes populated |
| Accuracy | 25% | % of CIs verified by Discovery in last 30 days |
| Timeliness | 20% | % of CIs discovered in last 7 days |
| Compliance | 15% | % of CIs CSDM-aligned |
| Relationships | 15% | % of CIs with at least one valid relationship |

### Scoring Thresholds
```
90–100  🟢 Excellent  — CMDB is fully trusted
75–89   🟡 Good       — Minor gaps, action recommended
60–74   🟠 Fair       — Significant gaps, improvement plan needed
Below 60 🔴 Poor      — CMDB cannot be trusted, urgent action required
```

### KPI Calculations

```javascript
// Script Include: CMDBHealthScorer
var CMDBHealthScorer = Class.create();
CMDBHealthScorer.prototype = {
    initialize: function() {},

    calculateCompletenessScore: function(ciClass) {
        var mandatoryFields = ['name', 'serial_number', 'manufacturer',
                               'model_id', 'location', 'owned_by'];
        var total = 0;
        var complete = 0;

        var gr = new GlideRecord(ciClass);
        gr.addQuery('install_status', '1'); // Installed only
        gr.query();

        while (gr.next()) {
            total++;
            var allFilled = mandatoryFields.every(function(field) {
                return gr[field].toString() !== '';
            });
            if (allFilled) complete++;
        }

        return total > 0 ? Math.round((complete / total) * 100) : 0;
    },

    calculateAccuracyScore: function(ciClass) {
        var cutoff = new GlideDateTime();
        cutoff.addDaysLocalTime(-30);

        var total = new GlideAggregate(ciClass);
        total.addQuery('install_status', '1');
        total.addAggregate('COUNT');
        total.query();
        var totalCount = total.next() ? parseInt(total.getAggregate('COUNT')) : 0;

        var verified = new GlideAggregate(ciClass);
        verified.addQuery('install_status', '1');
        verified.addQuery('last_discovered', '>=', cutoff);
        verified.addAggregate('COUNT');
        verified.query();
        var verifiedCount = verified.next() ? parseInt(verified.getAggregate('COUNT')) : 0;

        return totalCount > 0 ? Math.round((verifiedCount / totalCount) * 100) : 0;
    },

    getOverallScore: function(ciClass) {
        var completeness = this.calculateCompletenessScore(ciClass) * 0.25;
        var accuracy     = this.calculateAccuracyScore(ciClass) * 0.25;
        // Add other dimensions...
        return Math.round(completeness + accuracy);
    },

    type: 'CMDBHealthScorer'
};
```

---

## 🗺️ CI Relationship Types

### Key Relationships to Maintain

```
Server [Runs]──────────────► Software Installation
Server [Hosts]─────────────► Virtual Machine
Server [Connected to]──────► Network Switch
Application [Uses]─────────► Database
Application [Depends on]───► Application Service
Load Balancer [Routes to]──► Application Server
Application Service [Maps to]► Business Application
```

### Relationship Validation Script

```javascript
// Validate CI relationships are correctly typed
// Run as part of monthly governance review

var RelationshipValidator = Class.create();
RelationshipValidator.prototype = {
    initialize: function() {
        this.errors = [];
    },

    validateRelationships: function() {
        // Check: Every Application Instance should have a parent
        var gr = new GlideRecord('cmdb_ci_appl');
        gr.addQuery('install_status', '1');
        gr.query();

        while (gr.next()) {
            // Check for at least one "Runs on" relationship
            var rel = new GlideRecord('cmdb_rel_ci');
            rel.addQuery('child', gr.sys_id);
            rel.addQuery('type.name', 'Runs on::Runs');
            rel.query();

            if (!rel.next()) {
                this.errors.push({
                    ci: gr.getDisplayValue('name'),
                    issue: 'Application has no server relationship'
                });
            }
        }

        gs.info('Relationship validation complete. Issues: ' + this.errors.length);
        return this.errors;
    },

    type: 'RelationshipValidator'
};
```

---

## 📋 CMDB Governance Checklist

### Weekly Tasks
- [ ] Review Discovery logs for probe failures
- [ ] Check MID Server health (all servers green)
- [ ] Review new CIs created (validate classification)
- [ ] Check deduplication queue

### Monthly Tasks
- [ ] Run CMDB Health Dashboard review
- [ ] Calculate KPI scores per CI class
- [ ] Review stale CIs (no discovery > 60 days)
- [ ] Validate CI relationships report
- [ ] Review reconciliation rule effectiveness
- [ ] Data Steward meeting — review exceptions

### Quarterly Tasks
- [ ] Full CSDM alignment review
- [ ] Discovery schedule optimization
- [ ] Credential rotation for Discovery accounts
- [ ] Deduplication rule effectiveness review
- [ ] Executive CMDB health report

---

## 🚀 CMDB Improvement Roadmap

```
Phase 1 (Month 1–2): Foundation
  ├── MID Server deployment
  ├── Initial Discovery configuration
  ├── Reconciliation rules setup
  └── Baseline health score established

Phase 2 (Month 3–4): Enrichment
  ├── Service Mapping configuration
  ├── CSDM entity hierarchy built
  ├── Deduplication rules deployed
  └── Import Set integrations (SCCM, etc.)

Phase 3 (Month 5–6): Governance
  ├── CMDB Health Dashboard live
  ├── Data Steward process active
  ├── Automated stale CI cleanup
  └── KPI targets defined & tracked

Phase 4 (Ongoing): Continuous Improvement
  ├── Monthly health reviews
  ├── Pattern refinement
  ├── New CI class onboarding
  └── Relationship completeness improvement
```

---

## 👤 Author

**Swarup Kumar Namana**
Senior ServiceNow Developer & Platform Architect
Columbus, Ohio, USA

[![Portfolio](https://img.shields.io/badge/Portfolio-swarup--namana.netlify.app-b8922a?style=flat-square)](https://swarup-namana.netlify.app)
[![LinkedIn](https://img.shields.io/badge/LinkedIn-swarupnamana-0077B5?style=flat-square&logo=linkedin)](https://linkedin.com/in/swarupnamana)
[![Email](https://img.shields.io/badge/Email-swarupnamana03%40gmail.com-D14836?style=flat-square&logo=gmail)](mailto:swarupnamana03@gmail.com)

---

*Framework built from real enterprise CMDB governance programs. All client data abstracted. Patterns generalized for reuse.*
