SplunK_TA_cisco-esa-extras

## Super magic search!!!

<pre>
sourcetype=cisco:esa:textmail NOT "Custom Log Entry" 
| eventstats values(src_ip) AS src_ip BY icid
| eventstats values(dest_ip) AS dest_ip BY dcid
| stats values(internal_message_id) AS tmpinternal_message_id
values(icid) AS icid
values(action) AS action
values(file_name) AS file_name
values(message_subject) AS subject
values(signature) AS signature
values(signature_type) AS signature_extra
values(user) AS user
     values(sender) AS sender
     values(recipient) AS recipient
     values(message_size) AS message_size
     values(message_id) AS message_id
     values(src_ip) AS src_ip
     values(dest_ip) AS dest_ip
     values(dcid) AS dcid BY internal_message_id
| eval recipient_count=mvcount(recipient)
| eval internal_message_id=tmpinternal_message_id
| mvexpand internal_message_id
| eventstats values(tmpinternal_message_id) AS tmp BY internal_message_id
| eval msg=mvjoin(tmp, " ")
| rex field=sender "@(?<sender_domain>.*)"
| stats values(icid) AS icid
values(action) AS action
values(file_name) AS file_name
values(subject) AS subject
values(signature) AS signature
values(signature_extra) AS signature_extra
values(user) AS user
     values(sender) AS sender
     values(sender_domain) AS sender_domain
     values(recipient) AS recipient
     max(message_size) AS message_size
     max(recipient_count) AS recipient_count
     values(message_id) AS message_id
     values(dcid) AS dcid
     values(tmp) as internal_message_id
     values(src_ip) AS src_ip
     values(dest_ip) AS dest_ip BY msg

</pre>
