---
title: How API authentication works
---
# Introduction

This document will walk you through how API authentication is implemented in our system. The purpose is to ensure secure access to our API endpoints by validating user credentials and managing sessions.

We will cover:

1. The request flow for API authentication.
2. Handling of session creation and destruction.
3. Error management during authentication.

# Request flow for API authentication

<SwmSnippet path="/app/controllers/api/v1/authentications_controller.rb" line="1">

---

The <SwmToken path="/app/controllers/api/v1/authentications_controller.rb" pos="5:3:3" line-data="    class AuthenticationsController &lt; ApiController">`AuthenticationsController`</SwmToken> is responsible for managing authentication requests. It skips certain actions for the <SwmToken path="/app/controllers/api/v1/authentications_controller.rb" pos="6:12:12" line-data="      skip_before_action :find_current_session, only: [:create]">`create`</SwmToken> method to allow unauthenticated access when creating a session.

```
# frozen_string_literal: true

module Api
  module V1
    class AuthenticationsController < ApiController
      skip_before_action :find_current_session, only: [:create]
      skip_before_action :require_login, only: [:create]
```

---

</SwmSnippet>

<SwmSnippet path="/app/controllers/api/v1/authentications_controller.rb" line="9">

---

The <SwmToken path="/app/controllers/api/v1/authentications_controller.rb" pos="9:3:3" line-data="      def create">`create`</SwmToken> method is where the session is built from the provided credentials. If the credentials are invalid, an exception is raised.

```
      def create
        @session = Session.build_from_credential(session_params)
        raise BlackCandy::InvalidCredential unless @session.save
```

---

</SwmSnippet>

<SwmSnippet path="/app/controllers/api/v1/authentications_controller.rb" line="13">

---

If the <SwmToken path="/app/controllers/api/v1/authentications_controller.rb" pos="13:12:12" line-data="        login(@session) if params[:with_cookie]">`with_cookie`</SwmToken> parameter is present, the user is logged in with a session cookie.

```
        login(@session) if params[:with_cookie]
      end
```

---

</SwmSnippet>

# Session management

<SwmSnippet path="/app/controllers/api/v1/authentications_controller.rb" line="16">

---

The <SwmToken path="/app/controllers/api/v1/authentications_controller.rb" pos="16:3:3" line-data="      def destroy">`destroy`</SwmToken> method handles the termination of a session by destroying the current session.

```
      def destroy
        Current.session.destroy
      end
```

---

</SwmSnippet>

# Error handling

<SwmSnippet path="/app/controllers/application_controller.rb" line="22">

---

In the <SwmToken path="/app/controllers/application_controller.rb" pos="3:2:2" line-data="class ApplicationController &lt; ActionController::Base">`ApplicationController`</SwmToken>, we manage errors such as invalid credentials by rendering appropriate responses. This ensures that users receive clear feedback when authentication fails.

```
  rescue_from BlackCandy::InvalidCredential do |error|
    respond_to do |format|
      format.json { render_json_error(error, :unauthorized) }
      format.html { redirect_to new_session_path }
    end
  end
```

---

</SwmSnippet>

# Testing authentication

The test suite verifies various scenarios, such as creating an authentication without a session, with a session, and handling wrong credentials. This ensures the robustness of our authentication logic.

<SwmSnippet path="/test/controllers/api/v1/authentications_controller_test.rb" line="10">

---

For instance, the test for creating authentication without a session checks that no session ID is set in cookies, but an API token is returned.

```
  test "should create authentication without session" do
    post api_v1_authentication_url, as: :json, params: {
      session: {
        email: @user.email,
        password: "foobar"
      }
    }
```

---

</SwmSnippet>

<SwmSnippet path="/test/controllers/api/v1/authentications_controller_test.rb" line="27">

---

The test for creating authentication with a session ensures that a session ID is set in cookies, confirming successful session creation.

```
  end

  test "should create authentication with session" do
    post api_v1_authentication_url, as: :json, params: {
      with_cookie: true,
      session: {
        email: @user.email,
        password: "foobar"
      }
    }
```

---

</SwmSnippet>

<SwmSnippet path="/test/controllers/api/v1/authentications_controller_test.rb" line="47">

---

Tests also verify that incorrect credentials result in an unauthorized response, ensuring that invalid attempts are properly handled.

```
  end

  test "should not create authentication with wrong credential" do
    post api_v1_authentication_url, as: :json, params: {
      session: {
        email: "fake@email.com",
        password: "fake"
      }
    }
```

---

</SwmSnippet>

<SwmSnippet path="/test/controllers/api/v1/authentications_controller_test.rb" line="89">

---

Finally, the test for destroying authentication confirms that sessions are correctly terminated upon request.

```
  end

  test "should destroy authentication" do
    post api_v1_authentication_url, as: :json, params: {
      session: {
        email: @user.email,
        password: "foobar"
      }
    }
```

---

</SwmSnippet>

This walkthrough provides an overview of the API authentication process, focusing on the request flow, session management, and error handling.

<SwmMeta version="3.0.0" repo-id="Z2l0aHViJTNBJTNBYmMtdGVzdCUzQSUzQXRlc3Qtb3JnLTEyOTM3NzI=" repo-name="bc-test"><sup>Powered by [Swimm](https://app.swimm.io/)</sup></SwmMeta>
