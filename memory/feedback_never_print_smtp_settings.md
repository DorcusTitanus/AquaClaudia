---
name: Never print raw smtp_settings or other credential-bearing config hashes
description: Rails commands like `puts ActionMailer::Base.smtp_settings.inspect` dump the SMTP password in clear text. Mask credentials before printing, or use a command that doesn't include them.
type: feedback
---

When debugging Canvas / Rails SMTP (or any credential-bearing config), NEVER suggest commands that print the raw settings hash. `ActionMailer::Base.smtp_settings.inspect` includes `:password => "..."` in the output. The user pastes that output back into chat → the password ends up in the conversation transcript, Anthropic's prompt cache, and the user's local terminal history.

**Why:** Observed 2026-05-11. I asked Jefe to run `puts ActionMailer::Base.smtp_settings.inspect` to verify SMTP config loaded. He pasted the output, which contained the live `canvas@wilsonidt.com` password verbatim. Forced an immediate credential rotation. Avoidable with a masked check.

**How to apply:** When verifying credential-bearing Rails config, mask sensitive fields before printing. Pattern:

```ruby
s = ActionMailer::Base.smtp_settings.dup
s[:password] = s[:password] ? "[#{s[:password].length} chars]" : nil
puts s.inspect
```

Or check individual non-sensitive fields:

```ruby
puts "host=#{ActionMailer::Base.smtp_settings[:address]} port=#{ActionMailer::Base.smtp_settings[:port]} user=#{ActionMailer::Base.smtp_settings[:user_name]} pw_set=#{!ActionMailer::Base.smtp_settings[:password].nil?}"
```

Same rule applies to: database.yml dumps, security.yml dumps, OAuth client secrets, API tokens, JWK private keys, anything keyed on `:password`, `:secret`, `:key`, `:token`. Default to masked output unless the user has explicitly asked to see the raw value.
