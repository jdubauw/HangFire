Using Redis
============

HangFire with Redis job storage implementation processes jobs much faster than with SQL Server storage. On my development machine I observed more than 4x throughput improvement with empty jobs (method that does not do anything). ``HangFire.Redis`` leverages the ``BRPOPLPUSH`` command to fetch jobs, so the job processing latency is kept to minimum.

Please, see the `downloads page <http://redis.io/download>`_ to obtain latest version of Redis. If you unfamiliar with this great storage, please see its `documentation <http://redis.io/documentation>`_. 

Redis also supports Windows platform, but this is unofficial fork made by clever Microsoft guys. Here are GitHub repository branches for versions: `2.6 <https://github.com/MSOpenTech/redis/tree/2.6>`_, `2.8 <https://github.com/MSOpenTech/redis/tree/2.8>`_. Redis binaries are available through NuGet (`32-bit <https://www.nuget.org/packages/Redis-32/>`_, `64-bit <https://www.nuget.org/packages/Redis-64/>`_) and Chocolate galleries (`64-bit <http://chocolatey.org/packages/redis-64>`_ only). To install it as a Windows Service, check the `rgl/redis <https://github.com/rgl/redis>`_ repository, install it and update with binaries given above. *Don't use Redis 2.4 for Windows version for production environments (it is slow)*.

Installation
-------------

Redis job storage implementation is available through the `HangFire.Redis <https://www.nuget.org/packages/HangFire.Redis/>`_ NuGet package. So, install it using the NuGet Package Manager Console window:

.. code-block:: powershell

   PM> Install-Package HangFire.Redis

Configuration
--------------

If you have package ``HangFire`` or ``HangFire.Web`` installed, open the ``~/App_Start/HangFireConfig.cs`` file and add the following line:

.. code-block:: c#

   // Using hostname only and default port 6379
   JobStorage.Current = new RedisStorage("localhost");
   // or specify a port
   JobStorage.Current = new RedisStorage("localhost:6379");
   // or add a db number
   JobStorage.Current = new RedisStorage("localhost:6379", 0);
   // or use a password
   JobStorage.Current = new RedisStorage("password@localhost:6379", 0);

HangFire leverages connection pool to get connections quickly and shorten their usage. You can configure the pool size to match your environment needs:

.. code-block:: c#

   var options = new RedisStorageOptions
   {
       ConnectionPoolSize = 50 // default value
   };

   var storage = new RedisStorage("localhost", 0, options);