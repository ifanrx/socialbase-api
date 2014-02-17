======================
SocialBase RESTful API
======================

本文档描述 Socialbase RESTful API 的使用以及开发。

****
名词
****

* 服务端, SocialBase: SocialBase 服务所在的应用服务器
* 客户端: 与本接口协议兼容的应用服务器


********
授权
********

本接口使用 OAuth2 进行 `AAA` (Authentication, Authorization, Accounting)。 OAuth2 的具体实现符合 RFC 6749 的描述。

For all OAuth requests, the URL prefix is::

  https://api.socialbase.cn/api/oauth2/

Authorization Code Grant
========================

Authorization Request
---------------------

Parameters and behaviors are well documented in RFC 6749: `Authorization Request`.

The User-agent will be accessing ``authorize`` with the following parameters::

  code
  client_id

Descriptions could be found in section 4.1.1.

Behavior of ``redirect_url``
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

If ``redirect_url`` is not undefined, the ``code`` would be returned as a JSON string directly. Otherwise, we follow section 4.1.2.

As an proprietary add-on, we provide the following extra HTTP headers when ``redirect_url`` is not undefined::

  X-OAuth-Code: Grant_Code
  X-OAuth-Code-Expires-In: 600

Behavior upon error
~~~~~~~~~~~~~~~~~~~

Currently, behaviors are undefined. Don't expect to receive errors.

Access Token Request
--------------------

The client would initiate an `POST` request to ``access_token`` with the following parameters::

  grant_type=authorization_code
  code=GRANT_CODE
  client_id=YOUR_CLIENT_ID

Please user HTTP Basic authentication with ``client_id:client_secret`` pair. The current implementation follows section 4.1.3 and 4.1.4.


Refresh Token
=============

We follow section 6.


Accessing API
=============

The resources server will validate OAuth token presented in the request header. Carry `OAuth` authorization header with all requests to restricted resources::

    Authorization: OAuth ACCESS_TOKEN


***
API
***

API endpoint for Socialbase is::

  https://api.socialbase.cn/api/v1/


Accessing Resources
===================

Serialization Format
--------------------

For alternative data formats, append ``?format=FORMAT`` querystring. We currently support ``json`` and ``yaml``. XML support will come in the near future.

Read Only Access
----------------

To access read only resources, use HTTP `GET` method.

Getting A Collection Of Resources
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

By default, multiple record view is paginated. Metadata for current collection of resources is in `meta` array.

===================== =========================================================
 Field                 Description
===================== =========================================================
limit                  Limit items per record set.
next                   URI for next page, if any.
offset                 Current record set offset.
previous               URI for previous page, if any.
total_count            Total count in record set.
===================== =========================================================

All objects can be found under ``objects`` array.

Each resources/object has a resource_uri field that points to its detailed view.

To access data with offset of ``OFFSET``, append ``?offset=OFFSET`` to the query.

To access a subset of resouces, use::

  /set/START;END/

For example::

  wxuser/set/1;10/

returns a set of resources. There's no pagination applied.


Getting A Detail Resource
~~~~~~~~~~~~~~~~~~~~~~~~~

Access details for a single record with it's ``resources_uri``


Creating A New Resource
-----------------------

To create new data, use `POST` method. Note we require correct ``Content-type``.

On success, the HTTP code will be 20x and the server will provide a ``Location`` header. That's the detailed resouce view of this newly created object.

Updating An Existing Resource
-----------------------------

Use `PUT` method on detailed resource view. Requested payload will replace existing data.

Partially Updating An Existing Resource
---------------------------------------

Use `PATCH` method.

Updating A Whole Collection Of Resources
----------------------------------------

Use `PUT` method on resources collection view. The data structure would look contain following fields:


===================== =========================================================
 Field                 Description
===================== =========================================================
objects                A list of object dictionary.
===================== =========================================================

Deleting A Single Resource
--------------------------

Use `DELETE` method on detailed resource view.

Resources List
==============

enterprise
----------

Read only access to current enterprise information.


===================== =========================================================
 Field                 Description
===================== =========================================================
activate_plugins       Plugins activated by current enterprise.
admin_users            Email address for all administrators.
available_balance      Available balance for use by current enterprise.
balance                Balance for current enterprise.
id                     Internal ID for enterprise. Don't disclose to user.
is_pastdue             Account is past due. Past due accounts generally don't
                       have any access to any resources.
name                   Name of current enterprise
user_amount            Total amount of WeChat followers.
weixin_uid             WeChat internal user id.
===================== =========================================================

Returns::

  Single record view

Supported Methods::

  GET

wxuser
------

Read only access to WeChat followers.

(Not for public release yet.)


plugins/dianfm
--------------

Read/write access to `dian.fm` plugin.

===================== =========================================================
 Field                 Description
===================== =========================================================
channel_url            Channel URL.
created_at             Creation timestamp. Not required when creating new
                       object.
device_uuid            Universal Unique Identifier of the device used to create
                       this channel.
===================== =========================================================

Supported Methods::

  All methods.
