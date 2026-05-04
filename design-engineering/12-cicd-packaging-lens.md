# DE12. CI/CD and Packaging Lens

The CI/CD and Packaging lens looks for code and configuration that will fail in deployment, in a different org, or in a CI pipeline that runs without org data. This lens also checks whether the package metadata is complete and whether destructive changes are handled correctly.

## What this lens catches

- `package.xml` that does not include all required components
- `destructiveChanges.xml` for components that are removed but not tracked
- Deprecated classes still deployed and still referenced by active code
- Profiles deployed with field-level security that differs from the target org (causes deployment failures)
- Permission Sets with custom permissions not included in the deployment
- Named Credentials referenced in code but not included in the package
- Remote Site Settings missing from the deployment package
- Hardcoded org IDs or sandbox names in source code
- Source tracking files (.forceignore, .gitattributes) that hide deployed components
- Namespace prefix assumptions in managed package code
- API version mismatches between source files and target org
- Unlocked package metadata that is not unlocked-package-ready
- CI pipeline that uses `seeAllData=true` tests requiring production org data

## Required package checks

**package.xml completeness.** Every component referenced by the code must be in the package. If a class references a Custom Metadata Type, the Custom Metadata Type and its fields must be in the package. If a class uses a Named Credential, the Named Credential must be included.

**destructiveChanges.xml correctness.** When a component is removed from the package, it must also be in `destructiveChanges.xml` to ensure it is deleted from the target org. A component removed from the source but not from the package causes a deployment error. A component in `destructiveChanges.xml` but not previously deployed causes a deployment error.

**Profile and permission deployment.** Profiles are metadata-heavy and often fail deployment because the target org has different permission configurations. The safest pattern is to use Permission Sets instead of Profiles for application-specific permissions, and to avoid deploying Profiles unless the CI/CD pipeline is designed for it.

## Required questions

- Can this code deploy to a clean scratch org with no pre-existing data?
- Can this code deploy to a second sandbox with a different configuration?
- Is every Custom Metadata record and Permission Set included in the package?
- Does the CI pipeline run against a fresh scratch org or does it require existing org data?
- Are deprecated classes still consuming test coverage time?

## The clean deploy test

The acid test for CI/CD readiness: can you take the source, spin up a brand new scratch org, and deploy successfully with `sf project deploy start`?

If the answer is no, the CI/CD lens has found a failure. Document which components fail and why.

## What this chapter covered

- CI/CD failure patterns this lens detects
- Required package completeness checks
- The clean deploy test as the acid test for deployment readiness
- Severity rules for CI/CD findings

## References

- [Salesforce Deployment Guide](https://developer.salesforce.com/docs/atlas.en-us.sfdx_dev.meta/sfdx_dev/sfdx_dev_develop_mdapi.htm)
- [Unlocked Packages](https://developer.salesforce.com/docs/atlas.en-us.packagingDistLear.meta/packagingDistLear/packaging_intro.htm)
- [Salesforce CI/CD Best Practices](https://developer.salesforce.com/docs/atlas.en-us.sfdx_dev.meta/sfdx_dev/sfdx_dev_ci.htm)