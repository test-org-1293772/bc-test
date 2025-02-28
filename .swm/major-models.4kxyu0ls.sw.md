---
title: Major models
---
The <SwmToken path="/app/models/album.rb" pos="4:2:2" line-data="class Album &lt; ApplicationRecord # head model">`Album`</SwmToken> model is one of the most important. But it doesn't stand alone.  I

<SwmSnippet path="/app/models/album.rb" line="7">

---

It uses several concerns to encapsulate different parts of its logic.

```ruby
  include SearchableConcern
  include ImageableConcern
  include FilterableConcern
  include SortableConcern
```

---

</SwmSnippet>

&nbsp;

<SwmSnippet path="app/models/artist.rb" line="6">

---

Some similarity in Artists

```

  include SearchableConcern
  include ImageableConcern
  include SortableConcern

  validates :name, presence: true
  has_many :albums, dependent: :destroy
  has_many :songs

```

---

</SwmSnippet>

## User model

User's are also important.

<SwmSnippet path="/app/models/user.rb" line="24">

---

&nbsp;

```ruby
  has_many :playlists, -> { where(type: nil) }, inverse_of: :user, dependent: :destroy
  has_many :sessions, dependent: :destroy

  has_one :current_playlist, dependent: :destroy
  has_one :favorite_playlist, dependent: :destroy
```

---

</SwmSnippet>

&nbsp;

\\

&nbsp;

<SwmMeta version="3.0.0" repo-id="Z2l0aHViJTNBJTNBYmMtdGVzdCUzQSUzQXRlc3Qtb3JnLTEyOTM3NzI=" repo-name="bc-test"><sup>Powered by [Swimm](https://app.swimm.io/)</sup></SwmMeta>
