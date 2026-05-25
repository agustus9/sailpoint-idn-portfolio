# 🎛️ Advanced Access Governance, Forms Engine & App Porting

## 📋 Scenario & Business Problem
Enterprise environments frequently suffer from two operational bottlenecks: "role explosion" (having too many redundant, unmanaged access roles) and manual data entry errors during user onboarding. When a new contractor or employee joins, managers often have to input attributes manually. If a single required field is formatted incorrectly or left blank, downstream target application provisioning fails entirely. 

Furthermore, development teams need a structured way to migrate these complex form structures, application configurations, and metadata from Sandbox testing environments straight into production systems without manually clicking through browser screens.

## 💡 Solution Architecture
This project establishes an automated user intake and environment migration pipeline using **

SailPoint Identity Security Cloud (ISC)

** and modern API tools.
1. **Interactive Forms Engineering:** Built a declarative, validation-enforced User Intake Form schema to capture mandatory onboarding attributes before access workflows trigger.
2. **Access Profile & Role Mining:** Organized raw entitlements into consolidated, auditable Access Profiles to streamline business role mining.
3. **Environment Porting (Configuration Hub):** Automated the export and import of tenant system objects utilizing the native SailPoint Deployer framework to port configurations between Sandbox and Production environments safely.

## 🏗️ Technical Components
* **Component Type:** Declarative Forms Engine Schema & Deployment Packages
* **Tooling Integration:** VS Code, Postman Client Workspace, SailPoint Configuration Hub
* **API Standards:** REST Architecture, v3/Beta Configuration Deployer APIs, JSON Form Schemas

---

## 🛠️ Implementation Blueprints

### 1. Production User Intake Form Configuration (JSON Schema)
This declarative form engine schema builds an interactive input interface for managers during manual employee onboarding. It enforces technical string validation, ensuring the input matches corporate naming formats before initiating downstream provisioning tasks.

```json
{
  "name": "Contractor Onboarding Information Form",
  "description": "Captures and validates mandatory workforce identity attributes during manual provisioning requests.",
  "formInput": [
    {
      "id": "contractor_first_name",
      "label": "Legal First Name",
      "type": "string",
      "required": true,
      "validation": {
        "regex": "^[a-zA-Z\\s]{2,30}$",
        "errorMessage": "First name must contain only letters and be between 2 and 30 characters."
      }
    },
    {
      "id": "contractor_department",
      "label": "Target Department Code",
      "type": "string",
      "required": true,
      "allowedValues": [
        "Finance",
        "Security Operations",
        "Identity Engineering",
        "Sales"
      ]
    }
  ]
}
```

### 2. Configuration Hub Porting Script (VS Code REST Request)
This environment migration definition block ports verified forms, application configurations, and access profiles directly from a Sandbox tenant environment to a Production tenant environment using the configuration hub engine.

```http
### Step 1: Export Target Object Configuration Package from Sandbox
POST https://identitynow.com
Authorization: Bearer {{sandbox_bearer_token}}
Content-Type: application/json

{
  "description": "Exporting verified Contractor Onboarding Form and Access Profiles for production release",
  "objectTypes": [
    "FORM",
    "ACCESS_PROFILE"
  ]
}

### Step 2: Import and Compare Package inside Production Tenant
POST https://identitynow.com
Authorization: Bearer {{production_bearer_token}}
Content-Type: application/json

{
  "packageId": "pkg_987654321_v1",
  "options": {
    "previewOnly": true,
    "collisionResolution": "OVERWRITE"
  }
}
```

---

## 🚀 How to Import & Run
1. Copy the payload block inside `config/Forms-Deployment-Blueprint.json`.
2. Open the **Postman App** on your local machine or your **VS Code REST Client**.
3. Establish your target tenant endpoint parameters inside your active Environment profile configurations.
4. Execute the migration payload to port configurations or instantiate forms natively on the platform.
