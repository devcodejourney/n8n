<script setup lang="ts">
import { N8nActionDropdown, N8nButton, N8nText } from '@n8n/design-system';
import type { ActionDropdownItem } from '@n8n/design-system';
import { useI18n } from '@n8n/i18n';
import { computed } from 'vue';
import CredentialIcon from './CredentialIcon.vue';

interface Props {
	credentialTypeName: string;
	credentialName: string;
	isConnected: boolean;
	canModify: boolean;
}

defineProps<Props>();

const emit = defineEmits<{
	connect: [];
	modify: [];
	disconnect: [];
}>();

const i18n = useI18n();

const connectedActions = computed<Array<ActionDropdownItem<string>>>(() => [
	{
		id: 'modify',
		label: i18n.baseText('credentials.private.row.modify'),
	},
	{
		id: 'disconnect',
		label: i18n.baseText('credentials.private.row.disconnect'),
	},
]);

function onActionSelect(action: string) {
	if (action === 'modify') emit('modify');
	else if (action === 'disconnect') emit('disconnect');
}
</script>

<template>
	<div :class="$style.row">
		<div :class="$style.left">
			<CredentialIcon :credential-type-name="credentialTypeName" :size="20" />
			<div :class="$style.info">
				<N8nText size="small">{{ credentialName }}</N8nText>
				<div :class="$style.status">
					<template v-if="isConnected">
						<span :class="$style.dot" />
						<N8nText size="xsmall" color="text-light">
							{{ i18n.baseText('credentials.private.row.connectedStatus') }}
						</N8nText>
					</template>
					<template v-else>
						<span :class="$style.dotDisconnected" />
						<N8nText size="xsmall" color="text-light">
							{{ i18n.baseText('credentials.private.row.notConnected') }}
						</N8nText>
					</template>
				</div>
			</div>
		</div>

		<div :class="$style.right">
			<N8nButton
				v-if="!isConnected && canModify"
				size="small"
				variant="outline"
				:label="i18n.baseText('credentials.private.row.connect')"
				data-test-id="node-credential-private-connect"
				@click="emit('connect')"
			/>
			<N8nActionDropdown
				v-else-if="isConnected && canModify"
				:items="connectedActions"
				placement="bottom-end"
				data-test-id="node-credential-private-connected-actions"
				@select="onActionSelect"
			>
				<template #activator>
					<N8nButton
						size="small"
						variant="outline"
						:label="i18n.baseText('credentials.private.row.connected')"
						icon="chevron-down"
						icon-orientation="right"
					/>
				</template>
			</N8nActionDropdown>
		</div>
	</div>
</template>

<style lang="scss" module>
.row {
	display: flex;
	align-items: center;
	justify-content: space-between;
	gap: var(--spacing--xs);
	padding: var(--spacing--3xs) 0;
}

.left {
	display: flex;
	align-items: center;
	gap: var(--spacing--xs);
	min-width: 0;
	flex: 1;
}

.info {
	display: flex;
	flex-direction: column;
	min-width: 0;
}

.status {
	display: flex;
	align-items: center;
	gap: var(--spacing--5xs);
}

.dot {
	flex-shrink: 0;
	width: 8px;
	height: 8px;
	border-radius: 50%;
	background-color: var(--color--success);
}

.dotDisconnected {
	flex-shrink: 0;
	width: 8px;
	height: 8px;
	border-radius: 50%;
	background-color: var(--color--danger);
}

.right {
	flex-shrink: 0;
}
</style>
