# Async Background Notification

Allows to easily send workspace notifications to users with the workspace permission and itil.
Super useful in ssynchronous long running business rules or scheduled jobs.

## Example

### 1

A background script to send yourself a remonder to pay a visit to the beloved sys_user table

```
bmu = new x_716205_async_b_n.AsyncBMUtils()

var msgId = bmu.sendMessage(
  gs.getUserID(),
  "hi there",
  "Click here to go to the <a href=\"sys_user_list.do\">user table</a>",
  gs.getCurrentApplicationId(),
  gs.getCurrentScopeName(),
  2,
  "other"
)
```

### 2

Send a message to the user with the sys_id "a4b7a9f7"
`x_716205_async_b_n.AsyncBMUtils.sendMessage("a4b7a9f7","hi there","hi, this is a Message to <b>you</b>.")`

## System requirements

SanDiego or newer, older might work but untested.

## Licensing

Since this application uses a custom table, you may need to allocate it to a license.

## Bugs

Messages are retrieved by the client after x seconds, so it might take up to a minute/a page refresh for a user to receive your message.

## Docs

### sendMessage()

Send a message to a user by sys_id.

@returns sys_id of the generated message (for reference in _appendToMessage()_).

It is recommended to provide some more infos, such as

| argument            | type                                    | default  |
| ------------------- | --------------------------------------- | -------- |
| calling scope       | string of scope name                    |          |
| calling application | sys_id of current application           |          |
| priority            | number between 1 (critical) and 4 (low) | 4        |
| notification type   | string value                            | "others" |

#### Usage
```x_716205_async_b_n.AsyncBMUtils.sendMessage(user_sys_id, short_message, message_html, calling_application_sys_id, calling_scope, priority, notification_type)```

### appendToMessage()

Update a previously sent message.

This will change the message field and the short_description field respectively.

@returns result of message.update()

#### Usage

```x_716205_async_b_n.AsyncBMUtils.appendToMessage(message_sys_id, short_message, message_html, priority)```
