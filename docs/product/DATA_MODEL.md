# Data Model

## Conventions

- Table prefix shown as `tbl`; implementation uses Perfex DB prefix helpers.
- Primary keys `BIGINT UNSIGNED` where supported; timestamps UTC-compatible `DATETIME`.
- JSON columns may use `LONGTEXT` with strict encode/decode validation if deployed MySQL compatibility makes native JSON undesirable.
- No raw model/provider/Chatwoot/OpenBao/Vault secrets in these tables.
- Foreign relationships to Perfex core are logical unless Perfex migration conventions safely support FK constraints; indexes are mandatory.
- Archive/revoke/soft-delete is preferred for security/config records; audit records are append-only.

## `tblperfex_mcp_ai_profiles`
Runtime routing metadata.

- `id` bigint PK
- `name` varchar(191)
- `adapter` varchar(64) (`openclaw`, future adapters)
- `endpoint_ref` varchar(255) nullable (safe endpoint/config reference)
- `secret_ref` varchar(255) nullable (OpenBao handle/reference only)
- `model_profile` varchar(191) nullable
- `data_policy` text/json
- `budget_policy` text/json
- `status` varchar(32)
- `is_default` tinyint
- `created_by`, `updated_by` bigint nullable
- `created_at`, `updated_at`
Indexes: `(adapter,status)`, `(is_default)`.

## `tblperfex_mcp_conversations`
Native Ara conversation metadata/content according to retention policy.

- `id` bigint PK
- `principal_type` varchar(32), `principal_id` varchar(191)
- `staff_id` bigint nullable, `contact_id` bigint nullable, `customer_id` bigint nullable
- `surface` varchar(32) (`admin`,`client`)
- `title` varchar(255) nullable
- `runtime_session_ref` varchar(255) nullable
- `status` varchar(32)
- `retention_class` varchar(32)
- `last_activity_at`, `created_at`, `updated_at`, `archived_at` nullable
Indexes: principal, customer/contact, last activity.

## `tblperfex_mcp_messages`
Only native Ara messages/tool cards needed for the Perfex experience. **Do not mirror Chatwoot human transcripts wholesale.**

- `id` bigint PK
- `conversation_id` bigint
- `role` varchar(24)
- `content` longtext nullable
- `content_classification` varchar(32)
- `source_refs` text/json nullable
- `tool_run_id` bigint nullable
- `runtime_message_ref` varchar(255) nullable
- `created_at`
Indexes: `(conversation_id,id)`.
Retention/anonymization policy applies; audit does not rely on message body permanence.

## `tblperfex_mcp_context_links`
Explicit active/linked context references.

- `id`, `conversation_id`
- `entity_type` varchar(64), `entity_id` varchar(191)
- `customer_id`, `contact_id` nullable
- `visibility` varchar(24)
- `source_module` varchar(64)
- `created_at`, `removed_at` nullable
Unique active key on conversation/entity where practical.

## `tblperfex_mcp_ai_runs`
- `id`, `conversation_id` nullable
- principal identifiers/surface
- `profile_id` nullable
- `mode` varchar(24)
- `status` varchar(32)
- `runtime_ref` varchar(255) nullable
- `input_classification` varchar(32)
- `usage_json` text/json nullable
- `cost_minor` bigint nullable, `currency` varchar(8) nullable
- `started_at`, `completed_at` nullable
- `duration_ms`, `correlation_id`, `error_code` nullable
Indexes: status/time/principal/correlation.

## `tblperfex_mcp_tool_runs`
- `id`, `ai_run_id` nullable
- `tool_name`, `tool_version`, `owner_module`
- `risk_class`
- `target_type`, `target_id` nullable
- `status`
- `safe_input_summary`, `safe_output_summary` text nullable
- `source_freshness_json` nullable
- `duration_ms`, `correlation_id`, timestamps, `error_code`
Indexes: tool/status/time/target/correlation.

## `tblperfex_mcp_action_policies`
Overrides/exposure; definitions remain registered in code.

- `id`, `action_id`, `action_version` nullable
- `surface` varchar(24)
- `principal_type` varchar(32) nullable
- `enabled` tinyint
- `confirmation_mode` varchar(32)
- `approval_policy_ref` varchar(191) nullable
- `step_up_required` tinyint
- `constraints_json` text/json nullable
- `created_by`, `updated_by`, timestamps
Unique: `(action_id,surface,principal_type)`.

## `tblperfex_mcp_action_runs`
- `id`, `action_id`, `action_version`
- actor/principal fields
- target fields/customer scope
- `risk_class`, `status`
- `idempotency_key_hash` varchar(128) nullable
- `preview_ref`, `approval_ref` varchar(191) nullable
- `before_summary`, `after_summary` text/json nullable
- `correlation_id`, `started_at`, `completed_at`, `error_code`
Unique/index idempotency by principal/action scope; target/status/time indexes.

## `tblperfex_mcp_clients`
MCP client Principal metadata.

- `id` bigint PK
- `client_uid` char(36)/varchar unique
- `name`
- `auth_mode` varchar(32)
- `credential_fingerprint` varchar(128) nullable
- `secret_ref` varchar(255) nullable (OpenBao reference only)
- `status` (`active`,`disabled`,`expired`,`revoked`)
- `expires_at`, `last_used_at`, `rotated_at`, `revoked_at` nullable
- `rate_policy_json`, `restrictions_json`
- `created_by`, `created_at`, `updated_at`
Indexes: status/expiry/last-used.

## `tblperfex_mcp_client_scopes`
- `id`, `client_id`
- `scope` varchar(191)
- `resource_type` varchar(64) nullable
- `resource_id` varchar(191) nullable
- `customer_id` bigint nullable
- timestamps
Unique compound indexes preventing duplicate grants.

## `tblperfex_mcp_attention_items`
- `id`, `provider`, `type`, `dedupe_key`
- `title`, `reason`
- `priority`, `state`
- audience/principal/customer/contact/project/property refs nullable
- source entity/module/evidence refs/freshness
- `detected_at`, `due_at`, `snoozed_until`, `resolved_at` nullable
- `resolution_reason`, `primary_action_id`, `deep_link` nullable
Unique active dedupe key per provider/scope.

## `tblperfex_mcp_watches`
- `id`, `name`, owner principal fields
- `condition_type`, `condition_json`
- `source_module`, `target_scope_json`
- `trigger_mode` (`event`,`schedule`,`hybrid`)
- `action_id` nullable, `notification_policy_json` nullable
- `approval_policy_ref` nullable
- `status`, `last_checked_at`, `last_triggered_at`, `next_run_at` nullable
- timestamps
Indexes: status/next-run/source.

## `tblperfex_mcp_support_refs`
Safe Chatwoot mapping/snapshot only.

- `id`
- `customer_id`, `contact_id` nullable
- optional project/property/request refs
- `chatwoot_account_id`, `chatwoot_inbox_id`, `chatwoot_contact_id`, `chatwoot_conversation_id`
- `handoff_state` (`requested`,`queued`,`human_active`,`waiting_client`,`resolved`,`failed`)
- `status_snapshot`, `priority_snapshot`, `team_ref`, `agent_ref` nullable
- `handoff_reason`, `safe_summary` text nullable
- `source_freshness_at`, `last_sync_at`, `human_joined_at`, `resolved_at` nullable
- `correlation_id`, timestamps
Unique: Chatwoot account+conversation. No message body/transcript table.

## `tblperfex_mcp_audit_events`
Append-only accountability evidence.

- `id`, `event_type`, actor/principal/surface
- resource/target refs
- `safe_metadata_json`
- `correlation_id`, `request_id`, `ip_hash_or_safe_ip` nullable, `device_meta` nullable
- `created_at`
Indexes: event/time/actor/target/correlation. No update/delete in ordinary application paths.

## `tblperfex_mcp_updates`
Matches hardened updater history: source/target version, release tag, checksum, mode, status, backup path/reference, safe error, start/completion timestamps.

## Retention

- Audit: retained according to security/business policy; append-only.
- AI messages: configurable and minimised; deletion/anonymization must not erase required audit evidence.
- Tool/action operational logs: bounded retention with aggregated metrics.
- Support references: retain mapping/history as needed; no copied transcript.
- Revoked clients: metadata retained for audit; secret handle revoked/deleted in OpenBao policy.
