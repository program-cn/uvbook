
.. _request:

:c:type:`uv_req_t` --- Base request
===================================

`uv_req_t` is the base type for all libuv request types.

Structures are aligned so that any libuv request can be cast to `uv_req_t`.
All API functions defined here work with any request type.


Data types
----------

.. c:type:: uv_req_t

    The base libuv request structure.

.. c:type:: uv_any_req

    Union of all request types.


Public members
^^^^^^^^^^^^^^

.. c:member:: void* uv_request_t.data

    Space for user-defined arbitrary data. libuv does not use this field.

.. c:member:: uv_req_type uv_req_t.type

    Indicated the type of request. Readonly.

    ::

        typedef enum {
            UV_UNKNOWN_REQ = 0,
            UV_REQ,
            UV_CONNECT,
            UV_WRITE,
            UV_SHUTDOWN,
            UV_UDP_SEND,
            UV_FS,
            UV_WORK,
            UV_GETADDRINFO,
            UV_GETNAMEINFO,
            UV_REQ_TYPE_PRIVATE,
            UV_REQ_TYPE_MAX,
        } uv_req_type;


API
---

.. c:function:: int uv_cancel(uv_req_t* req)

    Cancel a pending request. Fails if the request is executing or has finished
    executing.

    Returns 0 on success, or an error code < 0 on failure.

    Only cancellation of :c:type:`uv_fs_t`, :c:type:`uv_getaddrinfo_t`,
    :c:type:`uv_getnameinfo_t` and :c:type:`uv_work_t` requests is
    currently supported.

    Cancelled requests have their callbacks invoked some time in the future.
    It's **not** safe to free the memory associated with the request until the
    callback is called.

    Here is how cancellation is reported to the callback:

    * A :c:type:`uv_fs_t` request has its req->result field set to `UV_ECANCELED`.

    * A :c:type:`uv_work_t`, :c:type:`uv_getaddrinfo_t` or c:type:`uv_getnameinfo_t`
      request has its callback invoked with status == `UV_ECANCELED`.

.. c:function:: size_t uv_req_size(uv_req_type type)

    Returns the size of the given request type. Useful for FFI binding writers
    who don't want to know the structure layout.
