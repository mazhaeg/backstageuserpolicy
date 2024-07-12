import { PermissionPolicy, PolicyAuthorizeQuery, PolicyDecision } from '@backstage/plugin-permission-common';
import { BackstageIdentityResponse } from '@backstage/plugin-auth-backend';
import { createCatalogPolicyDecision, catalogConditions } from '@backstage/plugin-catalog-backend';

class DemoPolicy implements PermissionPolicy {
    constructor(
        private readonly userGroupMap: Map<string, string[]> // Maps user IDs to their groups
    ) {}

    async handle(
        request: PolicyAuthorizeQuery,
        user?: BackstageIdentityResponse
    ): Promise<PolicyDecision> {
        if (request.permission.name === 'catalog.entity.view') {
            const userGroups = user?.groups ?? [];
            const entityOwnerId = request.entity.metadata?.annotations?.['backstage.io/created-by']; // Assuming entity creator ID is stored in annotations

            if (entityOwnerId) {
                const entityOwnerGroups = this.userGroupMap.get(entityOwnerId) ?? [];
                // Check if there's any common group between the user and the entity owner
                const hasCommonGroup = userGroups.some(group => entityOwnerGroups.includes(group));

                if (hasCommonGroup) {
                    return { result: 'ALLOW' };
                }
            }
        }

        // Default deny for other permissions
        return { result: 'DENY' };
    }
}
