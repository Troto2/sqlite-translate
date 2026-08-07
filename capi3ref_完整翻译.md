# C/C++ Interface For SQLite Version 3

> 原文来源：https://sqlite.org/capi3ref.html（sqlite.org）
>
> 本文是 SQLite 版本 3 的 C 语言接口规范（capi3ref）的完整中文翻译。
> 本部分为全篇共用的名词库 / 翻译对照表：对象、常量、函数名一律保留英文，
> 中文释义以本表为准，保证全文术语统一。

---

## 翻译对照表（名词库）



### 对象（Objects）



| 对象 | 中文释义 |

|------|----------|

| `sqlite3` | 数据库连接对象（`sqlite3_open()` 创建、`sqlite3_close()` 销毁） |

| `sqlite3_api_routines` | 可加载扩展的 API 函数指针表（扩展入口函数的第三参数） |

| `sqlite3_backup` | 在线备份对象（`sqlite3_backup_init()` 创建、`sqlite3_backup_finish()` 销毁） |

| `sqlite3_blob` | 增量 BLOB I/O 句柄 |

| `sqlite3_context` | SQL 函数执行上下文（自定义函数的第一参数） |

| `sqlite3_data_directory` | 数据库文件目录全局变量（仅 Windows VFS 使用） |

| `sqlite3_file` | OS 接口层打开的文件句柄 |

| `sqlite3_filename` | 文件名类型（VFS 的 `xOpen` 方法用） |

| `sqlite3_index_info` | 虚拟表索引信息结构（`xBestIndex` 用） |

| `sqlite3_int64` / `sqlite3_uint64` / `sqlite_int64` / `sqlite_uint64` | 64 位整数类型 |

| `sqlite3_io_methods` | OS 文件虚拟方法表（VFS 用） |

| `sqlite3_mem_methods` | 内存分配方法表（自定义分配器） |

| `sqlite3_module` | 虚拟表模块结构（注册虚拟表用） |

| `sqlite3_mutex` | 互斥锁句柄 |

| `sqlite3_mutex_methods` | 互斥锁方法表（自定义锁系统） |

| `sqlite3_pcache` | 自定义页缓存对象 |

| `sqlite3_pcache_methods2` | 页缓存方法表 v2 |

| `sqlite3_pcache_page` | 单个缓存页 |

| `sqlite3_snapshot` | 快照对象（`sqlite3_snapshot_get()` 创建） |

| `sqlite3_stmt` | 预编译语句对象 |

| `sqlite3_str` | 动态字符串构造器（`sqlite3_str_*` 用） |

| `sqlite3_temp_directory` | 临时文件目录全局变量 |

| `sqlite3_value` | 单个 SQL 值（函数参数/结果用） |

| `sqlite3_vfs` | 虚拟文件系统（OS 抽象层）对象 |

| `sqlite3_vtab` | 虚拟表实例对象 |

| `sqlite3_vtab_cursor` | 虚拟表游标对象 |



### 常量（Constants）



第一批（ch_1~ch_3 涉及）：



| 常量 | 中文释义 |

|------|----------|

| `SQLITE_SERIALIZE_NOCOPY` | `sqlite3_serialize()` 标志：直接返回当前使用的内存连续数据库副本指针、不做拷贝 |

| `SQLITE_SETLK_BLOCK_ON_CONNECT` | `sqlite3_setlk_timeout()` 标志：设置锁超时时若连接尚未完成则阻塞 |

| `SQLITE_SHM_NLOCK` | `xShmLock` 方法 "offset" 参数的上限（值为 8） |

| `SQLITE_INDEX_SCAN_HEX` | `SQLITE_INDEX_SCAN_*` 标志：令 `EXPLAIN QUERY PLAN` 以十六进制显示 idxNum |

| `SQLITE_INDEX_SCAN_UNIQUE` | `SQLITE_INDEX_SCAN_*` 标志：查询计划最多返回一行 |

| `SQLITE_INDEX_CONSTRAINT_*` | 虚拟表 WHERE 约束算子（EQ/NE/LT/LE/GT/GE/IS/ISNOT/ISNULL/ISNOTNULL/LIKE/GLOB/REGEXP/MATCH/FUNCTION/LIMIT/OFFSET） |

| `SQLITE_SYNC_NORMAL` | `xSync` 标志：普通 fsync() |

| `SQLITE_SYNC_FULL` | `xSync` 标志：Mac OS X 风格 fullsync |

| `SQLITE_SYNC_DATAONLY` | `xSync` 标志：只同步文件数据、不同步 inode |

| `SQLITE_LOCK_NONE` / `SQLITE_LOCK_SHARED` / `SQLITE_LOCK_RESERVED` / `SQLITE_LOCK_PENDING` / `SQLITE_LOCK_EXCLUSIVE` | `xLock()`/`xUnlock()` 的锁级别（无/共享/保留/待决/排他） |

| `SQLITE_IOERR_SHORT_READ` | 扩展错误码：读到的字节不足（`xRead` 须把未读部分填零） |

| `SQLITE_IOCAP_ATOMIC*` | 设备能力标志：任意大小的写操作原子（ATOMIC512~ATOMIC64K 表示 nnn 字节整块原子） |

| `SQLITE_IOCAP_SAFE_APPEND` | 设备能力标志：追加数据先写内容后扩展文件大小 |

| `SQLITE_IOCAP_SEQUENTIAL` | 设备能力标志：数据按 `xWrite()` 调用顺序写入磁盘 |

| `SQLITE_IOCAP_UNDELETABLE_WHEN_OPEN` | 设备能力标志：文件打开期间不可删除 |

| `SQLITE_IOCAP_POWERSAFE_OVERWRITE` | 设备能力标志：断电安全的覆盖写 |

| `SQLITE_IOCAP_IMMUTABLE` | 设备能力标志：文件不可变 |

| `SQLITE_IOCAP_BATCH_ATOMIC` | 设备能力标志：支持批量原子写 |

| `SQLITE_IOCAP_SUBPAGE_READ` | 设备能力标志：支持子页读取 |

| `SQLITE_CONFIG_MALLOC` | `sqlite3_config()` 选项：指定自定义内存分配器 |

| `SQLITE_CONFIG_GETMALLOC` | `sqlite3_config()` 选项：查询当前内存分配器 |

| `SQLITE_CONFIG_MEMSTATUS` | `sqlite3_config()` 选项：内存状态统计开关 |

| `SQLITE_CONFIG_MUTEX` | `sqlite3_config()` 选项：指定自定义互斥锁实现 |

| `SQLITE_CONFIG_GETMUTEX` | `sqlite3_config()` 选项：查询当前互斥锁实现 |

| `SQLITE_MUTEX_STATIC_MAIN` | 静态互斥锁：主（main）互斥锁 |

| `SQLITE_MUTEX_STATIC_MEM` | 静态互斥锁：内存分配互斥锁 |

| `SQLITE_OK` | 结果码：成功（值为 0） |

| `SQLITE_CONSTRAINT` | 结果码：约束冲突（值为 19） |

| `SQLITE_OPEN_READWRITE` | `sqlite3_open_v2()` 标志：读写方式打开 |

| `SQLITE_OPEN_CREATE` | `sqlite3_open_v2()` 标志：不存在则创建 |

| `SQLITE_OPEN_READONLY` | `sqlite3_open_v2()` 标志：只读方式打开 |

| `SQLITE_OPEN_DELETEONCLOSE` | 打开标志：关闭时删除文件 |

| `SQLITE_OPEN_MAIN_DB` | 打开标志：主数据库文件 |

| `SQLITE_OPEN_MAIN_JOURNAL` | 打开标志：主数据库日志文件 |

| `SQLITE_OPEN_TEMP_DB` | 打开标志：临时数据库文件 |

| `SQLITE_OPEN_TEMP_JOURNAL` | 打开标志：临时数据库日志文件 |

| `SQLITE_OPEN_TRANSIENT_DB` | 打开标志：瞬态数据库文件 |

| `SQLITE_OPEN_SUBJOURNAL` | 打开标志：子日志文件 |

| `SQLITE_OPEN_SUPER_JOURNAL` | 打开标志：超级日志文件 |

| `SQLITE_OPEN_WAL` | 打开标志：WAL 文件 |

| `SQLITE_OPEN_EXCLUSIVE` | 打开标志：与 SQLITE_OPEN_CREATE 联用，文件必须新建、已存在则报错（对应 POSIX O_EXCL） |
| `SQLITE_ACCESS_EXISTS` | `xAccess` 标志：测试文件是否存在 |
| `SQLITE_ACCESS_READWRITE` | `xAccess` 标志：测试文件是否可读可写 |
| `SQLITE_ACCESS_READ` | `xAccess` 标志：测试文件是否至少可读（内置 VFS 未实现） |
| `SQLITE_CANTOPEN` | 结果码：无法打开文件 |
| `SQLITE_ABORT` | 结果码：操作被中止（如过期 BLOB 句柄） |
| `SQLITE_ERROR` | 结果码：一般错误 |
| `SQLITE_READONLY` | 结果码：只读（如对只读打开的 BLOB 写入） |
| `SQLITE_BUSY` | 结果码：数据库被锁定、忙 |
| `SQLITE_MISUSE` | 结果码：API 误用 |
| `SQLITE_ROW` | 结果码：sqlite3_step() 返回一行（值为 100） |
| `SQLITE_DONE` | 结果码：sqlite3_step() 执行完成（值为 101） |
| `SQLITE_OPEN_MAIN_JOURNAL` | 打开标志：主数据库日志文件 |
| `SQLITE_OPEN_WAL` | 打开标志：WAL 文件 |
| `SQLITE_DESERIALIZE_READONLY` | 反序列化标志：只读（禁止 SQLite 向内存数据库追加内容） |
| `SQLITE_DESERIALIZE_FREEONCLOSE` | 反序列化标志：连接关闭时用 sqlite3_free() 释放序列化缓冲区 |
| `SQLITE_DESERIALIZE_RESIZEABLE` | 反序列化标志：允许用 sqlite3_realloc64() 增大缓冲区 |
| `SQLITE_DBCONFIG_ENABLE_LOAD_EXTENSION` | 数据库连接配置：仅启用 C-API 扩展加载（禁用 load_extension() SQL 函数，更安全） |
| `SQLITE_OPEN_SHAREDCACHE` | 打开标志：为单个连接启用共享缓存模式 |
| `SQLITE_CONFIG_LOG` | sqlite3_config() 选项：设置错误日志回调（sqlite3_log() 写入） |
| `SQLITE_RESULT_SUBTYPE` | 函数标志：函数可设置结果子类型（配合 sqlite3_result_subtype()） |
| `SQLITE_ERROR_SNAPSHOT` | 扩展错误码：快照已被检查点覆盖，无法打开 |
| `SQLITE_PROTOCOL` | 结果码：协议错误 |
| `SQLITE_IOERR` | 结果码：I/O 错误（值为 10） |
| `SQLITE_INTERRUPT` | 结果码：操作被中断（值为 9） |
| `SQLITE_SCHEMA` | 结果码：schema 已改变，需重新准备语句 |
| `SQLITE_CORRUPT` | 结果码：数据库文件损坏（值为 11） |
| `SQLITE_TXN_NONE` / `SQLITE_TXN_READ` / `SQLITE_TXN_WRITE` | 事务状态：无事务/读事务/写事务（由低到高） |
| `SQLITE_LOCKED_SHAREDCACHE` | 扩展错误码：共享缓存锁被阻塞（SQLITE_LOCKED 的扩展） |
| `SQLITE_INSERT` / `SQLITE_DELETE` / `SQLITE_UPDATE` | 更新钩子事件：插入/删除/更新 |
| `SQLITE_TRACE_*` | 跟踪事件常量（STMT/PROFILE/ROW/CLOSE，sqlite3_trace_v2 用） |
| `SQLITE_STMTSTATUS_*` | 语句状态计数器（RUN/SORT/AUTOINDEX/VM_STEP/FULLSCAN_STEP 等，sqlite3_stmt_status 用） |
| `SQLITE_LIMIT_LENGTH` | 运行时限制类别：SQL 字符串最大长度 |
| `SQLITE_MAX_LENGTH` | SQLITE_LIMIT_LENGTH 的编译期硬上限 |
| `SQLITE_FCNTL_PERSIST_WAL` | 文件控制：连接全部关闭后 WAL 文件是否保留在磁盘 |
| `SQLITE_UTF8` / `SQLITE_UTF16LE` / `SQLITE_UTF16BE` / `SQLITE_UTF16` / `SQLITE_ANY` / `SQLITE_UTF16_ALIGNED` / `SQLITE_UTF8_ZT` | 文本编码常量（1/2/3/4/5/8/16） |
| `SQLITE_INTEGER` / `SQLITE_FLOAT` / `SQLITE_TEXT` / `SQLITE_BLOB` / `SQLITE_NULL` / `SQLITE3_TEXT` | 五大基本数据类型（1/2/3/4/5） |
| `SQLITE_SUBTYPE` | 函数标志：函数参数/结果带子类型（配合 value_subtype/result_subtype） |
| `SQLITE_ROLLBACK` / `SQLITE_IGNORE` / `SQLITE_FAIL` / `SQLITE_REPLACE` | ON CONFLICT 冲突解决策略 |
| `SQLITE_NOTFOUND` | 结果码：未找到（如 sqlite3_file_control 未知 opcode） |
| `SQLITE_FULL` | 结果码：数据库已满（值为 13） |
| `SQLITE_TOOBIG` | 结果码：字符串或 BLOB 超过大小限制（值为 18） |
| `SQLITE_MISMATCH` | 结果码：数据类型不匹配（值为 20） |
| `SQLITE_PERM` | 结果码：访问权限被拒绝（值为 3） |
| `SQLITE_INTERNAL` | 结果码：SQLite 内部逻辑错误（值为 2） |
| `SQLITE_NOLFS` | 结果码：使用宿主不支持的操作系统特性（值为 22） |
| `SQLITE_AUTH` | 结果码：授权被拒绝（值为 23） |
| `SQLITE_FORMAT` | 结果码：未使用（值为 24） |
| `SQLITE_RANGE` | 结果码：sqlite3_bind 第 2 参数越界（值为 25） |
| `SQLITE_NOTADB` | 结果码：打开的文件不是数据库文件（值为 26） |
| `SQLITE_NOTICE` / `SQLITE_WARNING` | 结果码：sqlite3_log() 的通知/警告（27/28） |
| `SQLITE_EMPTY` | 结果码：仅供内部使用（值为 16） |
| `SQLITE_CARRAY_INT32` / `SQLITE_CARRAY_INT64` / `SQLITE_CARRAY_DOUBLE` / `SQLITE_CARRAY_TEXT` / `SQLITE_CARRAY_BLOB` | carray 表值函数的数据类型（0~4） |
| `SQLITE_DEFAULT_WAL_AUTOCHECKPOINT` | 新连接自动检查点的默认阈值（1000 页） |
| `SQLITE_CREATE_INDEX` / `SQLITE_CREATE_TABLE` / `SQLITE_DROP_*` / `SQLITE_INSERT` / `SQLITE_DELETE` / `SQLITE_UPDATE` / `SQLITE_READ` / `SQLITE_SELECT` / `SQLITE_TRANSACTION` / `SQLITE_ATTACH` / `SQLITE_DETACH` / `SQLITE_ALTER_TABLE` / `SQLITE_REINDEX` / `SQLITE_ANALYZE` / `SQLITE_CREATE_VTABLE` / `SQLITE_DROP_VTABLE` / `SQLITE_FUNCTION` / `SQLITE_SAVEPOINT` / `SQLITE_PRAGMA` / `SQLITE_RECURSIVE` / `SQLITE_COPY` | 授权回调动作码（sqlite3_set_authorizer 用） |
| `SQLITE_NOMEM` | 结果码：malloc() 失败、内存不足（值为 7） |
| `SQLITE_LOCKED` | 结果码：数据库中的表被锁定（值为 6） |
| `SQLITE_DBCONFIG_*` | 数据库连接配置选项（sqlite3_db_config 第二参数）：MAINDBNAME/LOOKASIDE/ENABLE_FKEY/ENABLE_TRIGGER/ENABLE_VIEW/ENABLE_FTS3_TOKENIZER/ENABLE_LOAD_EXTENSION/NO_CKPT_ON_CLOSE/ENABLE_QPSG/TRIGGER_EQP/RESET_DATABASE/DEFENSIVE/WRITABLE_SCHEMA/LEGACY_ALTER_TABLE/DQS_DML/DQS_DDL/LEGACY_FILE_FORMAT/TRUSTED_SCHEMA/STMT_SCANSTATUS/REVERSE_SCANORDER/ENABLE_ATTACH_CREATE/ENABLE_ATTACH_WRITE/ENABLE_COMMENTS/FP_DIGITS |
| `SQLITE_DETERMINISTIC` | 函数标志：相同输入参数时输出相同（可用于部分索引 WHERE 子句、生成列等） |
| `SQLITE_DIRECTONLY` | 函数标志：只能从顶层 SQL 调用，不能用于视图/触发器/schema 结构（推荐给有副作用或可能泄露敏感信息的函数） |
| `SQLITE_INNOCUOUS` | 函数标志：无害函数（无副作用、只依赖输入参数） |
| `SQLITE_SELFORDER1` | 函数标志：自身有序函数（可消除一个排序步骤） |
| `SQLITE_DENY` / `SQLITE_IGNORE` | 授权器返回码：拒绝（报错中止）/忽略（不允许但不报错） |
| `SQLITE_CONFIG_SORTERREF_SIZE` | 配置选项：排序引用大小阈值（需要 SQLITE_ENABLE_SORTER_REFERENCES） |
| `SQLITE_CONFIG_MEMDB_MAXSIZE` | 配置选项：sqlite3_deserialize() 创建的内存数据库默认最大大小（默认 1073741824） |
| `SQLITE_CONFIG_ROWID_IN_VIEW` | 配置选项：是否允许 VIEW 有 ROWID（需要 -DSQLITE_ALLOW_ROWID_IN_VIEW） |
| `SQLITE_DBCONFIG_MAX` | SQLITE_DBCONFIG 选项最大值（哨兵） |
| `SQLITE_VTAB_INNOCUOUS` | 虚拟表标志：无害虚拟表（可在触发器/视图中使用，即使 TRUSTED_SCHEMA 关闭） |
| `SQLITE_VTAB_DIRECTONLY` | 虚拟表标志：只能从顶层 SQL 调用 |
| `SQLITE_VTAB_USES_ALL_SCHEMAS` | 虚拟表标志：使用所有 schema |
| `SQLITE_FCNTL_*` | 标准文件控制操作码（xFileControl/sqlite3_file_control 用）：LOCKSTATE/SIZE_HINT/SIZE_LIMIT/CHUNK_SIZE/FILE_POINTER/JOURNAL_POINTER/SYNC/COMMIT_PHASETWO/PERSIST_WAL/POWERSAFE_OVERWRITE/PRAGMA/BUSYHANDLER/TEMPFILENAME/MMAP_SIZE/TRACE/HAS_MOVED/WIN32_*_HANDLE/WAL_BLOCK/ZIPVFS/RBU/VFSNAME/VFS_POINTER/BEGIN_ATOMIC_WRITE/COMMIT_ATOMIC_WRITE/ROLLBACK_ATOMIC_WRITE/LOCK_TIMEOUT/DATA_VERSION/CKPT_START/CKPT_DONE/RESERVE_BYTES/EXTERNAL_READER/CKSM_FILE/RESET_CACHE/NULL_IO/BLOCK_ON_CONNECT/FILESTAT 等 |
| `SQLITE_PREPARE_PERSISTENT` / `SQLITE_PREPARE_NORMALIZE` / `SQLITE_PREPARE_NO_VTAB` / `SQLITE_PREPARE_DONT_LOG` / `SQLITE_PREPARE_FROM_DDL` | 预编译标志（sqlite3_prepare_v3/prepare16_v3 的 prepFlags 参数） |
| `SQLITE_SCANSTAT_*` | 扫描状态度量（sqlite3_stmt_scanstatus 的 T 参数）：NLOOP/NVISIT/EST/NAME/EXPLAIN/SELECTID/PARENTID/NCYCLE |
| `SQLITE_MUTEX_FAST` / `SQLITE_MUTEX_RECURSIVE` | 互斥锁类型：快速/递归 |
| `SQLITE_MUTEX_STATIC_*` | 静态互斥锁（APP1~3/LRU/MEM/OPEN/PRNG/MAIN/VFS1~3/PEM 等，sqlite3_mutex_alloc 用） |
| `SQLITE_SHM_UNLOCK` / `SQLITE_SHM_LOCK` / `SQLITE_SHM_SHARED` / `SQLITE_SHM_EXCLUSIVE` | xShmLock 方法锁操作标志（1/2/4/8） |
| `SQLITE_STATIC` / `SQLITE_TRANSIENT` | 特殊析构函数值：内容指针恒定不销毁/内容将改变需复制 |
| `SQLITE_VERSION` / `SQLITE_VERSION_NUMBER` / `SQLITE_SOURCE_ID` | 编译期版本宏：版本字符串/版本整数 (X*1000000+Y*1000+Z)/源码标识 |
| `SQLITE_SHM_NLOCK` | xShmLock 方法的 offset 参数上限（值为 8） |
| `SQLITE_STATUS_*` | sqlite3_status() 运行时状态参数：MEMORY_USED/MALLOC_SIZE/MALLOC_COUNT/PAGECACHE_USED/PAGECACHE_OVERFLOW/PAGECACHE_SIZE/PARSER_STACK（SCRATCH_* 已废弃） |
| `SQLITE_SYNC_NORMAL` / `SQLITE_SYNC_FULL` / `SQLITE_SYNC_DATAONLY` | xSync 同步标志：普通 fsync/fullsync/只刷数据 |
| `SQLITE_TESTCTRL_*` | sqlite3_test_control() 操作码（仅供测试 SQLite，应用勿用） |
| `SQLITE_TRACE_STMT` / `SQLITE_TRACE_PROFILE` / `SQLITE_TRACE_ROW` / `SQLITE_TRACE_CLOSE` | sqlite3_trace_v2 跟踪事件类（0x01/0x02/0x04/0x08） |
| `SQLITE_TXN_NONE` / `SQLITE_TXN_READ` / `SQLITE_TXN_WRITE` | 事务状态（0/1/2，sqlite3_txn_state 返回值） |
| `SQLITE_VTAB_CONSTRAINT_SUPPORT` / `SQLITE_VTAB_INNOCUOUS` / `SQLITE_VTAB_DIRECTONLY` / `SQLITE_VTAB_USES_ALL_SCHEMAS` | 虚拟表配置选项（sqlite3_vtab_config 用） |
| `SQLITE_WIN32_DATA_DIRECTORY_TYPE` / `SQLITE_WIN32_TEMP_DIRECTORY_TYPE` | Win32 目录类型（sqlite3_win32_set_directory 用） |
| `SQLITE_LIMIT_*` | 运行时限制类别：LENGTH/SQL_LENGTH/COLUMN/EXPR_DEPTH/COMPOUND_SELECT/VDBE_OP/FUNCTION_ARG/ATTACHED/LIKE_PATTERN_LENGTH/VARIABLE_NUMBER/TRIGGER_DEPTH/WORKER_THREADS/PARSER_DEPTH |
| `SQLITE_DBSTATUS_*` | 数据库连接状态参数（sqlite3_db_status 第二参数）：LOOKASIDE_USED/CACHE_USED/SCHEMA_USED/STMT_USED/LOOKASIDE_HIT/CACHE_HIT/CACHE_MISS/CACHE_WRITE/DEFERRED_FKS/CACHE_USED_SHARED/CACHE_SPILL/TEMPBUF_SPILL |
| `SQLITE_STMTSTATUS_*` | 预编译语句状态计数器（sqlite3_stmt_status 用）：FULLSCAN_STEP/SORT/AUTOINDEX/VM_STEP/REPREPARE/RUN/FILTER_HIT/FILTER_MISS/MEMUSED |


### 函数（Functions）



第一批（ch_1~ch_3 涉及）：



| 函数 | 中文释义 |

|------|----------|

| `sqlite3_serialize()` | 把数据库序列化为内存字节串 |

| `sqlite3_deserialize()` | 用内存字节串初始化内存连续的数据库副本 |

| `sqlite3_setlk_timeout()` | 设置阻塞锁超时（毫秒） |

| `sqlite3_backup_init()` | 初始化在线备份对象 |

| `sqlite3_backup_finish()` | 结束在线备份并释放对象 |

| `sqlite3_result_*()` | SQL 函数结果报告系列（blob/double/int/text/value 等） |

| `sqlite3_aggregate_context()` | 取聚合函数上下文 |

| `sqlite3_user_data()` | 取自定义函数注册时的 pApp 指针 |

| `sqlite3_context_db_handle()` | 取函数执行所在的数据库连接 |

| `sqlite3_get_auxdata()` / `sqlite3_set_auxdata()` | 取/存函数辅助数据 |

| `sqlite3_filename_database()` | 取规范文件名中的数据库部分 |

| `sqlite3_filename_journal()` | 取规范文件名中的日志部分 |

| `sqlite3_filename_wal()` | 取规范文件名中的 WAL 部分 |

| `sqlite3_uri_parameter()` | 取 URI 文件名中的查询参数 |

| `sqlite3_uri_boolean()` | 取 URI 参数并解析为布尔值 |

| `sqlite3_uri_int64()` | 取 URI 参数并解析为 64 位整数 |

| `sqlite3_uri_key()` | 遍历 URI 参数名 |

| `sqlite3_declare_vtab()` | 声明虚拟表的 schema（CREATE TABLE 语句） |

| `sqlite3_vtab_collation()` | 返回虚拟表约束所用的排序规则 |

| `sqlite3_vtab_distinct()` | 返回虚拟表查询的 DISTINCT 处理方式 |

| `sqlite3_vtab_rhs_value()` | 返回虚拟表约束的右值 |

| `sqlite3_file_control()` | 对打开的文件执行文件控制操作 |

| `sqlite3_config()` | 全局配置（内存/锁/页缓存等） |

| `sqlite3_malloc()` / `sqlite3_realloc()` | SQLite 内存分配/重分配 |

| `sqlite3_shutdown()` | 关闭 SQLite 子系统 |

| `sqlite3_initialize()` | 初始化 SQLite 子系统 |

| `sqlite3_mutex_alloc()` | 分配互斥锁 |

| `sqlite3_mutex_free()` | 释放互斥锁 |

| `sqlite3_mutex_enter()` | 进入（加锁）互斥锁 |

| `sqlite3_mutex_try()` | 尝试进入互斥锁（不阻塞） |

| `sqlite3_mutex_leave()` | 离开（解锁）互斥锁 |

| `sqlite3_mutex_held()` | 判断当前线程是否持有互斥锁 |

| `sqlite3_mutex_notheld()` | 判断当前线程是否未持有互斥锁 |

| `sqlite3_libversion_number()` | 返回 SQLite 版本号（整数，如 3008002） |

| `sqlite3_vfs_register()` | 注册 VFS 模块 |

| `sqlite3_vfs_unregister()` | 取消注册 VFS 模块 |

| `sqlite3_vfs_find()` | 按名字查找 VFS 模块 |

| `sqlite3_open()` / `sqlite3_open16()` / `sqlite3_open_v2()` | 打开数据库连接 |

| `sqlite3_mprintf()` | SQLite 的格式化字符串分配函数 |

| `sqlite3_bind_parameter_count()` | 预编译语句中 SQL 参数的个数（最大参数索引） |
| `sqlite3_bind_parameter_index()` | 按参数名查参数索引 |
| `sqlite3_bind_parameter_name()` | 按参数索引查参数名（UTF-8） |
| `sqlite3_bind_zeroblob()` | 绑定零填充 BLOB 参数 |
| `sqlite3_blob_bytes()` | 返回打开的 BLOB 句柄对应 BLOB 的字节数 |
| `sqlite3_blob_close()` | 关闭 BLOB 句柄（无条件关闭） |
| `sqlite3_blob_open()` | 打开 BLOB 句柄做增量 I/O |
| `sqlite3_blob_read()` | 从打开的 BLOB 增量读取数据 |
| `sqlite3_blob_reopen()` | 把 BLOB 句柄移到同一表的另一行 |
| `sqlite3_blob_write()` | 向打开的 BLOB 增量写入数据 |
| `sqlite3_busy_timeout()` | 设置忙超时（表锁定时睡眠，累积 ms 毫秒后返回 SQLITE_BUSY） |
| `sqlite3_busy_handler()` | 设置自定义忙处理器（busy_timeout 会清除先前设置的处理器） |
| `sqlite3_cancel_auto_extension()` | 取消自动加载的静态链接扩展（成功取消注册返回 1，不在列表返回 0） |
| `sqlite3_clear_bindings()` | 把所有宿主参数重置为 NULL |
| `sqlite3_column_count()` | 返回预编译语句结果集的列数 |
| `sqlite3_data_count()` | 返回结果集当前行的列数 |
| `sqlite3_database_file_object()` | 由日志/WAL 文件名返回主数据库文件的 sqlite3_file 对象 |
| `sqlite3_db_cacheflush()` | 事务中把脏页刷写到磁盘（所有 schema） |
| `sqlite3_db_config()` | 配置单个数据库连接 |
| `sqlite3_db_filename()` | 返回连接上某数据库的文件名（绝对路径） |
| `sqlite3_db_handle()` | 返回预编译语句所属的数据库连接 |
| `sqlite3_db_mutex()` | 返回串行化数据库连接访问的互斥锁（Serialized 模式） |
| `sqlite3_db_name()` | 返回连接上第 N 个数据库的 schema 名 |
| `sqlite3_db_readonly()` | 判断数据库是否只读（1=只读 0=读写 -1=不存在） |
| `sqlite3_errcode()` / `sqlite3_errmsg()` | 取最近一次错误的错误码/错误消息 |
| `sqlite3_auto_extension()` | 注册静态链接扩展，在每次新建连接时自动加载 |
| `sqlite3_autovacuum_pages()` | 注册 autovacuum 压缩量回调（每次 autovacuum 前调用） |
| `sqlite3_reset_auto_extension()` | 清空自动扩展列表 |
| `sqlite3_create_function()` / `sqlite3_create_function16()` | 注册自定义 SQL 函数 |
| `sqlite3_os_init()` | 自定义 OS 层初始化例程 |
| `sqlite3_prepare_v2()` / `sqlite3_prepare16_v2()` / `sqlite3_prepare16_v3()` | 编译 SQL 为预编译语句 |
| `sqlite3_step()` | 执行预编译语句到下一行或完成 |
| `sqlite3_result_zeroblob()` | 把函数结果设为零填充 BLOB |
| `sqlite3_free()` | 释放 sqlite3_malloc()/mprintf 等分配的内存 |
| `sqlite3_db_release_memory()` | 尽量释放数据库连接 D 的堆内存（无需 SQLITE_ENABLE_MEMORY_MANAGEMENT） |
| `sqlite3_release_memory()` | 释放 N 字节堆内存（需要 SQLITE_ENABLE_MEMORY_MANAGEMENT，返回实际释放字节数） |
| `sqlite3_drop_modules()` | 移除数据库连接上除名单外所有虚拟表模块 |
| `sqlite3_create_module()` | 注册虚拟表模块 |
| `sqlite3_enable_load_extension()` | 启用/禁用扩展加载（同时作用于 C-API 和 load_extension() SQL 函数） |
| `sqlite3_load_extension()` | 从共享库文件加载扩展 |
| `sqlite3_enable_shared_cache()` | 启用/禁用进程级共享页缓存（不推荐使用） |
| `sqlite3_exec()` | 一步执行多条 SQL 的便捷包装（内部用 prepare_v2/step/finalize） |
| `sqlite3_column_text()` / `sqlite3_column_name()` | 取结果列文本/列名 |
| `sqlite3_extended_result_codes()` | 启用/禁用扩展结果码（默认禁用） |
| `sqlite3_finalize()` | 销毁预编译语句（必须对每条语句调用以避免泄漏） |
| `sqlite3_last_insert_rowid()` | 返回连接上最近一次成功 INSERT 的 rowid |
| `sqlite3_set_last_insert_rowid()` | 显式设置 last_insert_rowid 值 |
| `sqlite3_limit()` | 设置/查询各类构造的大小限制（按连接） |
| `sqlite3_log()` | 向错误日志写入消息（配合 SQLITE_CONFIG_LOG） |
| `sqlite3_snprintf()` | SQLite 的格式化字符串函数（snprintf 变体） |
| `sqlite3_next_stmt()` | 返回连接上下一个预编译语句（遍历语句列表） |
| `sqlite3_overload_function()` | 为虚拟表创建可被重载的函数占位符 |
| `sqlite3_progress_handler()` | 设置进度回调（长查询时定期调用，可实现取消按钮） |
| `sqlite3_randomness()` | 获取 SQLite 内置 PRNG 的随机字节 |
| `sqlite3_reset()` | 把预编译语句重置回初始状态（不重置绑定） |
| `sqlite3_reset_auto_extension()` | 禁用所有先前注册的自动扩展 |
| `sqlite3_result_subtype()` | 设置 SQL 函数结果的子类型（保留低 8 位） |
| `sqlite3_malloc64()` | 64 位内存分配 |
| `sqlite3_set_errmsg()` | 显式设置数据库句柄的错误码和错误消息 |
| `sqlite3_sleep()` | 挂起当前线程至少指定毫秒数 |
| `sqlite3_snapshot_cmp()` | 比较两个快照句柄的先后（需要 SQLITE_ENABLE_SNAPSHOT） |
| `sqlite3_snapshot_get()` | 获取数据库的 WAL 快照 |
| `sqlite3_snapshot_free()` | 销毁快照对象（避免内存泄漏） |
| `sqlite3_snapshot_open()` | 在历史快照 P 上开始（或升级）读事务 |
| `sqlite3_snapshot_recover()` | 扫描 WAL 文件，让所有有效快照可用（连接全部关闭后 WAL 残留时） |
| `sqlite3_soft_heap_limit()` / `sqlite3_soft_heap_limit64()` | 设置软堆内存上限（soft_heap_limit 已废弃） |
| `sqlite3_stmt_busy()` | 判断预编译语句是否已调用过 sqlite3_step() 但未完成也未 reset |
| `sqlite3_stmt_explain()` | 改变预编译语句的 EXPLAIN 设置（0=普通 1=EXPLAIN 2=EXPLAIN QUERY PLAN） |
| `sqlite3_stmt_isexplain()` | 查询预编译语句的 EXPLAIN 设置（返回 0/1/2） |
| `sqlite3_stmt_readonly()` | 判断预编译语句是否不会直接修改数据库文件 |
| `sqlite3_stmt_scanstatus_reset()` | 清零 sqlite3_stmt_scanstatus() 事件计数器（需要 SQLITE_ENABLE_STMT_SCANSTATUS） |
| `sqlite3_stmt_status()` | 取/重置预编译语句的 SQLITE_STMTSTATUS 计数器 |
| `sqlite3_str_new()` | 创建新的 sqlite3_str 动态字符串对象 |
| `sqlite3_str_finish()` | 结束 sqlite3_str 对象、取回字符串（并释放对象） |
| `sqlite3_str_errcode()` | 取 sqlite3_str 对象的错误码 |
| `sqlite3_str_length()` | 取 sqlite3_str 对象的当前长度 |
| `sqlite3_strglob()` | 判断字符串是否匹配 GLOB 模式（匹配返回 0，区分大小写） |
| `sqlite3_strlike()` | 判断字符串是否匹配 LIKE 模式（带转义字符，不区分大小写） |
| `sqlite3_stricmp()` / `sqlite3_strnicmp()` | 不区分大小写字符串比较（8 位） |
| `sqlite3_system_errno()` | 返回最近一次 I/O 错误/打开文件失败对应的操作系统错误码 |
| `sqlite3_table_column_metadata()` | 取表列的元数据（声明类型/排序规则/非空/主键/自增） |
| `sqlite3_test_control()` | 测试接口：读取内部状态、注入故障（仅用于测试 SQLite） |
| `sqlite3_threadsafe()` | 返回 SQLite 的编译期线程安全设置（0=未编互斥锁） |
| `sqlite3_trace_v2()` / `sqlite3_trace()` / `sqlite3_profile()` | 设置 SQL 跟踪/性能分析回调（trace/profile 已废弃） |
| `sqlite3_txn_state()` | 返回数据库的当前事务状态（SQLITE_TXN_*） |
| `sqlite3_unlock_notify()` | 注册解锁通知回调（共享缓存锁被解除时调用，需 SQLITE_ENABLE_UNLOCK_NOTIFY） |
| `sqlite3_update_hook()` | 注册数据更新回调（rowid 表行被增删改时调用） |
| `sqlite3_commit_hook()` / `sqlite3_rollback_hook()` / `sqlite3_preupdate_hook()` | 提交/回滚/预更新回调 |
| `sqlite3_close()` | 关闭数据库连接（close_v2 为新版接口） |
| `sqlite3_value_encoding()` | 报告 sqlite3_value 对象的内部文本编码（SQLITE_UTF8/UTF16BE/UTF16LE） |
| `sqlite3_value_subtype()` | 取 SQL 函数参数的子类型（配合 SQLITE_SUBTYPE） |
| `sqlite3_vtab_config()` | 配置虚拟表接口的各个方面（须在 xConnect/xCreate 内调用） |
| `sqlite3_vtab_in()` | 让 xBestIndex 一次性处理 IN 约束的所有值（代替逐个值调用 xFilter） |
| `sqlite3_vtab_in_first()` / `sqlite3_vtab_in_next()` | 遍历 IN 约束右侧的所有值 |
| `sqlite3_vtab_nochange()` | 判断虚拟表列访问是否属于不会改变列值的 UPDATE |
| `sqlite3_vtab_on_conflict()` | 返回触发虚拟表 xUpdate 的语句的 ON CONFLICT 策略 |
| `sqlite3_wal_autocheckpoint()` | 设置自动检查点阈值（WAL 日志帧数达到 N 时提交后自动检查点） |
| `sqlite3_wal_checkpoint()` | 执行被动检查点（等价于 checkpoint_v2 的 PASSIVE 模式） |
| `sqlite3_wal_checkpoint_v2()` | 以指定模式执行检查点（PASSIVE/FULL/RESTART/TRUNCATE/NOOP） |
| `sqlite3_wal_hook()` | 注册 WAL 提交回调（每次 wal 模式提交时调用） |
| `sqlite3_stmt_scanstatus_v2()` | 取预编译语句的扫描状态统计（需要 SQLITE_ENABLE_STMT_SCANSTATUS） |
| `sqlite3_normalized_sql()` | 返回预编译语句的规范化 SQL 文本 |
| `sqlite3_total_changes()` / `sqlite3_total_changes64()` | 返回连接上所有语句（含触发器/内部）影响的总行数 |
| `sqlite3_stmt_scanstatus()` | 取预编译语句某查询元素的扫描状态度量（需要 SQLITE_ENABLE_STMT_SCANSTATUS） |
| `sqlite3_sourceid()` | 返回 SQLite 源码标识字符串 |
| `sqlite3_libversion()` | 返回 SQLite 版本字符串 |
| `sqlite3_memory_used()` / `sqlite3_memory_highwater()` | 返回当前已用/历史最高内存分配字节数 |
| `sqlite3_db_status()` / `sqlite3_db_status64()` | 取数据库连接的状态参数（SQLITE_DBSTATUS_*） |
| `sqlite3_changes()` / `sqlite3_changes64()` | 返回最近一次语句影响的行数 |
| `sqlite3_get_autocommit()` | 判断连接是否处于自动提交模式 |
| `sqlite3_errstr()` | 返回错误码对应的静态错误消息字符串 |
| `sqlite3_error_offset()` | 返回最近一次语法错误的字节偏移 |
| `sqlite3_extended_errcode()` | 返回最近一次错误的扩展错误码 |
| `sqlite3_collation_needed()` / `sqlite3_collation_needed16()` | 注册排序规则请求回调 |
| `sqlite3_create_collation()` / `sqlite3_create_collation16()` / `sqlite3_create_collation_v2()` | 注册自定义排序规则 |
| `sqlite3_free_table()` | 释放 sqlite3_get_table() 分配的内存 |
| `sqlite3_get_clientdata()` / `sqlite3_set_clientdata()` | 取/设连接的客户端数据 |
| `sqlite3_create_window_function()` | 注册聚合窗口函数 |
| `sqlite3_create_module_v2()` | 注册虚拟表模块（带析构回调） |
| `sqlite3_close_v2()` | 新版关闭连接（延迟析构） |
| `sqlite3_interrupt()` | 中断连接上的挂起数据库操作 |
| `sqlite3_is_interrupted()` | 判断连接上的操作是否已被中断 |
| `sqlite3_preupdate_old()` / `sqlite3_preupdate_new()` | 取预更新钩子中被改行的旧/新值 |
| `sqlite3_backup_step()` | 在源和目标数据库之间复制最多 N 页 |
| `sqlite3_backup_remaining()` | 返回最近一次 backup_step 后剩余待备份页数 |
| `sqlite3_backup_pagecount()` | 返回源数据库的总页数 |
| `sqlite3_carray_bind()` / `sqlite3_carray_bind_v2()` | 把数组绑定到 carray 表值函数的参数 |
| `sqlite3_value_dup()` / `sqlite3_value_free()` | 复制/释放 sqlite3_value 对象 |
| `sqlite3_value_frombind()` | 判断值是否来自绑定参数 |
| `sqlite3_value_numeric_type()` | 若可能把值转换为数值类型并返回其类型 |
| `sqlite3_expanded_sql()` | 返回展开所有绑定参数后的 SQL 文本 |
| `sqlite3_sql()` | 返回预编译语句的原始 SQL 文本 |
| `sqlite3_str_append()` / `sqlite3_str_appendall()` / `sqlite3_str_appendf()` / `sqlite3_str_appendchar()` / `sqlite3_str_vappendf()` | 向 sqlite3_str 对象追加文本 |
| `sqlite3_str_value()` | 取 sqlite3_str 对象当前字符串内容 |
| `sqlite3_str_reset()` / `sqlite3_str_truncate()` / `sqlite3_str_free()` | 重置/截断/释放 sqlite3_str 对象 |
| `sqlite3_aggregate_count()` / `sqlite3_expired()` / `sqlite3_transfer_bindings()` / `sqlite3_global_recover()` / `sqlite3_thread_cleanup()` / `sqlite3_memory_alarm()` | 已废弃函数（为向后兼容保留，不解释用途） |
| `sqlite3_column_database_name()` / `sqlite3_column_database_name16()` | 返回结果列来源的数据库名 |
| `sqlite3_column_table_name()` / `sqlite3_column_table_name16()` | 返回结果列来源的表名 |
| `sqlite3_column_origin_name()` / `sqlite3_column_origin_name16()` | 返回结果列来源的列名（需 SQLITE_ENABLE_COLUMN_METADATA） |
| `sqlite3_column_decltype()` / `sqlite3_column_decltype16()` | 返回结果列的声明数据类型 |
| `sqlite3_compileoption_used()` / `sqlite3_compileoption_get()` | 编译期选项诊断：是否使用某选项/遍历选项列表 |
| `sqlite3_complete()` / `sqlite3_complete16()` | 判断输入文本是否构成完整的 SQL 语句 |
| `sqlite3_column_blob()` / `sqlite3_column_double()` / `sqlite3_column_int()` / `sqlite3_column_int64()` / `sqlite3_column_text()` / `sqlite3_column_text16()` / `sqlite3_column_value()` / `sqlite3_column_bytes()` / `sqlite3_column_bytes16()` / `sqlite3_column_type()` | 取结果列的值/大小/类型（blob/double/int/int64/text/text16/value/bytes/bytes16/type） |


---

## sqlite3_serialize 的标志（Flags for sqlite3_serialize）

```
#define SQLITE_SERIALIZE_NOCOPY 0x001   /* 不做内存分配 */
```

以下常量可零个或多个 OR 起来，作为 `sqlite3_serialize(D,S,P,F)` 的 F 参数。

`SQLITE_SERIALIZE_NOCOPY` 表示 `sqlite3_serialize()` 将直接返回它当前正在使用的
内存连续数据库副本的指针，而不拷贝该数据库。如果 SQLite 当前没有使用内存连续的
数据库，则此选项会导致 `sqlite3_serialize()` 返回 NULL 指针。只有当数据库先前经
`sqlite3_deserialize()` 初始化后，SQLite 才会使用内存连续的数据库。

## sqlite3_setlk_timeout() 的标志（Flags for sqlite3_setlk_timeout()）

```
#define SQLITE_SETLK_BLOCK_ON_CONNECT 0x01
```

## xShmLock 索引上限（Maximum xShmLock index）

```
#define SQLITE_SHM_NLOCK        8
```

`sqlite3_io_methods` 上的 xShmLock 方法可使用 0 到此上限之间的值作为其
"offset" 参数。SQLite 核心绝不会尝试在此范围之外获取或释放锁。

## 可加载扩展 Thunk（Loadable Extension Thunk）

```
typedef struct sqlite3_api_routines sqlite3_api_routines;
```

指向不透明 `sqlite3_api_routines` 结构的指针，作为第三个参数传给可加载扩展的
入口点。为了规避某些平台上的编译器警告，此结构必须被 typedef。

## 在线备份对象（Online Backup Object）

```
typedef struct sqlite3_backup sqlite3_backup;
```

`sqlite3_backup` 对象记录一次进行中的在线备份操作的状态信息。该对象由对
`sqlite3_backup_init()` 的调用创建，由对 `sqlite3_backup_finish()` 的调用销毁。

参见：使用 SQLite 在线备份 API

## SQL 函数上下文对象（SQL Function Context Object）

```
typedef struct sqlite3_context sqlite3_context;
```

SQL 函数执行时的上下文存储在一个 `sqlite3_context` 对象中。指向
`sqlite3_context` 对象的指针总是应用自定义 SQL 函数的第一个参数。应用自定义
SQL 函数的实现会把这个指针传给 `sqlite3_result()`、`sqlite3_aggregate_context()`、
`sqlite3_user_data()`、`sqlite3_context_db_handle()`、`sqlite3_get_auxdata()`
和/或 `sqlite3_set_auxdata()` 调用。

使用此对象的 26 个方法：

- sqlite3_aggregate_context
- sqlite3_context_db_handle
- sqlite3_get_auxdata
- sqlite3_result_blob
- sqlite3_result_blob64
- sqlite3_result_double
- sqlite3_result_error
- sqlite3_result_error16
- sqlite3_result_error_code
- sqlite3_result_error_nomem
- sqlite3_result_error_toobig
- sqlite3_result_int
- sqlite3_result_int64
- sqlite3_result_null
- sqlite3_result_pointer
- sqlite3_result_subtype
- sqlite3_result_text
- sqlite3_result_text16
- sqlite3_result_text16be
- sqlite3_result_text16le
- sqlite3_result_text64
- sqlite3_result_value
- sqlite3_result_zeroblob
- sqlite3_result_zeroblob64
- sqlite3_set_auxdata
- sqlite3_user_data

## 存放数据库文件的文件夹名（Name Of The Folder Holding Database Files）

```
SQLITE_EXTERN char *sqlite3_data_directory;
```

如果把这个全局变量指向一个字符串，该字符串是某个文件夹（即目录）的名字，那么
当 SQLite 使用内建的 Windows VFS 时，所有用相对路径名指定并被创建或访问的
数据库文件，都将被认为相对于那个目录。如果此变量是 NULL 指针，则 SQLite 认为
所有用相对路径名指定的数据库文件都相对于进程的当前目录。只有 Windows VFS 使用
这个全局变量；unix VFS 会忽略它。

在某个数据库连接处于打开状态时改变此变量的值，可能导致数据库损坏。

在多个线程中同时读取或修改此变量是不安全的；如果另一个线程同时在使用某个数据库
连接，那么读取或修改此变量也是不安全的。此变量的本意是：在进程初始化阶段、
任何 SQLite 接口例程被调用之前设置一次，此后保持不变。

data_store_directory pragma 可能修改此变量，使其指向由 `sqlite3_malloc` 获得的内存。
此外，data_store_directory pragma 总是假定此变量指向的字符串存放在由
`sqlite3_malloc` 获得的内存里，并且该 pragma 可能尝试用 `sqlite3_free` 释放那块
内存。因此，如果直接修改此变量，要么把它置为 NULL，要么让它指向由
`sqlite3_malloc` 获得的内存，否则应避免使用 data_store_directory pragma。

## OS 接口打开文件句柄（OS Interface Open File Handle）

```
typedef struct sqlite3_file sqlite3_file;
struct sqlite3_file {
  const struct sqlite3_io_methods *pMethods;  /* 打开文件的方法 */
};
```

`sqlite3_file` 对象代表 OS 接口层中的一个打开的文件。各个 OS 接口实现通常想
通过追加供自己使用的额外字段来继承（subclass）此对象。pMethods 条目是指向一个
`sqlite3_io_methods` 对象的指针，该对象定义了在打开的文件上执行 I/O 操作的方法。

## 文件名（File Name）

```
typedef const char *sqlite3_filename;
```

SQLite 用 `sqlite3_filename` 类型把文件名传给 VFS 的 xOpen 方法。它可以被转换为
`(const char*)` 并当作一个普通的、以 NUL 结尾的、包含文件名的 UTF-8 缓冲区来
处理，但也可以传给诸如以下的特殊 API：

- sqlite3_filename_database()
- sqlite3_filename_journal()
- sqlite3_filename_wal()
- sqlite3_uri_parameter()
- sqlite3_uri_boolean()
- sqlite3_uri_int64()
- sqlite3_uri_key()

## 虚拟表索引信息（Virtual Table Indexing Information）

```
struct sqlite3_index_info {
  /* 输入（Inputs） */
  int nConstraint;           /* aConstraint 中的条目数 */
  struct sqlite3_index_constraint {
     int iColumn;              /* 被约束的列。ROWID 为 -1 */
     unsigned char op;         /* 约束算子 */
     unsigned char usable;     /* 此约束是否可用（True） */
     int iTermOffset;          /* 内部使用——xBestIndex 应忽略 */
  } *aConstraint;            /* WHERE 子句约束表 */
  int nOrderBy;              /* ORDER BY 子句中的项数 */
  struct sqlite3_index_orderby {
     int iColumn;              /* 列号 */
     unsigned char desc;       /* DESC 为 True，ASC 为 False */
  } *aOrderBy;               /* ORDER BY 子句 */
  /* 输出（Outputs） */
  struct sqlite3_index_constraint_usage {
    int argvIndex;           /* 若 >0，该约束作为 argv 的一部分传给 xFilter */
    unsigned char omit;      /* 不为该约束生成测试代码 */
  } *aConstraintUsage;
  int idxNum;                /* 用于标识索引的编号 */
  char *idxStr;              /* 字符串，可能从 sqlite3_malloc 获得 */
  int needToFreeIdxStr;      /* 为真时用 sqlite3_free() 释放 idxStr */
  int orderByConsumed;       /* 若输出已按要求排序则为 True */
  double estimatedCost;           /* 使用此索引的估计代价 */
  /* 以下字段仅在 SQLite 3.8.2 及以后可用 */
  sqlite3_int64 estimatedRows;    /* 预计返回的行数 */
  /* 以下字段仅在 SQLite 3.9.0 及以后可用 */
  int idxFlags;              /* SQLITE_INDEX_SCAN_* 标志的掩码 */
  /* 以下字段仅在 SQLite 3.10.0 及以后可用 */
  sqlite3_uint64 colUsed;    /* 输入：语句用到的列的掩码 */
};
```

`sqlite3_index_info` 结构及其子结构是虚拟表接口的一部分，用于把信息传入虚拟表
模块的 xBestIndex 方法，并接收其返回结果。**输入（Inputs）** 下的字段是传给
xBestIndex 的输入，只读；xBestIndex 把结果填入**输出（Outputs）**字段。

aConstraint[] 数组记录如下形式的 WHERE 子句约束：

```
column OP expr
```

其中 OP 是 =、或 >=。具体的算子用某个 `SQLITE_INDEX_CONSTRAINT_` 值存放在
aConstraint[].op 中；列的索引存放在 aConstraint[].iColumn 中；当右侧的 expr
可以被求值（因而该约束可用）时 aConstraint[].usable 为 TRUE，否则为 false。

优化器会自动反转 "expr OP column" 形式的项，并对 WHERE 子句做其它简化，尽量让
尽可能多的 WHERE 子句项变成上面所示的形式。aConstraint[] 数组只报告与正在查询
的那个特定虚拟表相关的 WHERE 子句项。

关于 ORDER BY 子句的信息存放在 aOrderBy[] 中。aOrderBy 的每一项记录 ORDER BY
子句的一列。

colUsed 字段指示当前扫描可能需要虚拟表的哪些列。虚拟表列从零开始、按传给
`sqlite3_declare_vtab()` 的 CREATE TABLE 语句中出现的顺序编号。对于前 63 列
（列 0~62），如果该列可能被 SQLite 需要，则在 colUsed 掩码中置相应位；如果表
至少有 64 列、且第 63 列右边任一列被需要，则 colUsed 的位 63 也被置位。换句话说，
当表达式 `(colUsed & ((sqlite3_uint64)1 << (iCol>=63 ? 63 : iCol)))` 求值为
非零时，列 iCol 可能被需要。

xBestIndex 方法必须用要传给 xFilter 的参数信息填充 aConstraintUsage[]。如果
argvIndex>0，则对应 aConstraint[] 的右侧被求值。

## 虚拟表索引信息（续）

并成为 argv 的第 argvIndex 项。如果 aConstraintUsage[].omit 为真，则假定该约束已
完全由虚拟表处理，字节码可能不再检查它。aConstraintUsage[].omit 标志是一个优化
提示：当 omit 标志保持默认的 false 时，字节码中总会单独检查该约束；当 omit 改为
true 时，字节码中可能检查、也可能不检查该约束。换句话说，omit 为真时并不保证该
约束不会再被字节码检查。

idxNum 和 idxStr 值会被记录并传入 xFilter 方法。当且仅当 needToFreeIdxStr
为真时，用 `sqlite3_free()` 释放 idxStr。

orderByConsumed 表示 xFilter/xNext 的输出将以满足 ORDER BY 子句的正确顺序
产生，因此不再需要单独的排序步骤。

estimatedCost 值是对某个特定策略代价的估计。代价为 N 表示该策略的开销类似于
对含 N 行的 SQLite 表做线性扫描；代价为 log(N) 表示该操作的开销类似于对含 N
行的 SQLite 表的某个唯一索引字段做二分查找。

estimatedRows 值是对该策略将返回的行数的估计。

xBestIndex 方法可以可选地用 `SQLITE_INDEX_SCAN_*` 标志的掩码填充 idxFlags 字段。
其中一个标志是 `SQLITE_INDEX_SCAN_HEX`，设置后会让 EXPLAIN QUERY PLAN 的输出以
十六进制而非十进制显示 idxNum。另一个标志是 `SQLITE_INDEX_SCAN_UNIQUE`，设置后
表示该查询计划至多返回一行。

此外，如果 xBestIndex 设置了 `SQLITE_INDEX_SCAN_UNIQUE` 标志，SQLite 还假定：
如果在同一条语句里为删除或更新虚拟表行而调用了 xUpdate() 方法，且实现返回
`SQLITE_CONSTRAINT`，则无需回滚任何数据库改动。换句话说，若 xUpdate() 返回
`SQLITE_CONSTRAINT`，数据库内容必须与调用 xUpdate 之前完全一致。相反，如果未设置
`SQLITE_INDEX_SCAN_UNIQUE` 且 xUpdate 返回 `SQLITE_CONSTRAINT`，则 xUpdate 方法
所做的任何数据库改动都会由 SQLite 自动回滚。

> 注意：estimatedRows 字段是在 SQLite 版本 3.8.2（2013-12-06）时加入
> `sqlite3_index_info` 结构的。如果虚拟表扩展配合早于 3.8.2 的 SQLite 版本使用，
> 读取或写入 estimatedRows 字段的结果未定义（很可能导致应用崩溃）。因此只有当
> `sqlite3_libversion_number()` 返回值大于或等于 3008002 时才应使用
> estimatedRows 字段。类似地，idxFlags 字段是版本 3.9.0（2015-10-14）加入的，
> 因此只有 `sqlite3_libversion_number()` 返回值大于或等于 3009000 时才可使用。

使用此对象的 3 个方法：

- sqlite3_vtab_collation()
- sqlite3_vtab_distinct()
- sqlite3_vtab_rhs_value()

## OS 接口文件虚拟方法对象（OS Interface File Virtual Methods Object）

```
typedef struct sqlite3_io_methods sqlite3_io_methods;
struct sqlite3_io_methods {
  int iVersion;
  int (*xClose)(sqlite3_file*);
  int (*xRead)(sqlite3_file*, void*, int iAmt, sqlite3_int64 iOfst);
  int (*xWrite)(sqlite3_file*, const void*, int iAmt, sqlite3_int64 iOfst);
  int (*xTruncate)(sqlite3_file*, sqlite3_int64 size);
  int (*xSync)(sqlite3_file*, int flags);
  int (*xFileSize)(sqlite3_file*, sqlite3_int64 *pSize);
  int (*xLock)(sqlite3_file*, int);
  int (*xUnlock)(sqlite3_file*, int);
  int (*xCheckReservedLock)(sqlite3_file*, int *pResOut);
  int (*xFileControl)(sqlite3_file*, int op, void *pArg);
  int (*xSectorSize)(sqlite3_file*);
  int (*xDeviceCharacteristics)(sqlite3_file*);
  /* 以上方法对版本 1 有效 */
  int (*xShmMap)(sqlite3_file*, int iPg, int pgsz, int, void volatile**);
  int (*xShmLock)(sqlite3_file*, int offset, int n, int flags);
  void (*xShmBarrier)(sqlite3_file*);
  int (*xShmUnmap)(sqlite3_file*, int deleteFlag);
  /* 以上方法对版本 2 有效 */
  int (*xFetch)(sqlite3_file*, sqlite3_int64 iOfst, int iAmt, void **pp);
  int (*xUnfetch)(sqlite3_file*, sqlite3_int64 iOfst, void *p);
  /* 以上方法对版本 3 有效 */
  /* 未来版本可能增加更多方法 */
};
```

由 `sqlite3_vfs.xOpen` 方法打开的每个文件，都会用一个指向本对象实例的指针填充
`sqlite3_file` 对象（更常见的是其子类对象）。本对象定义了用于对 `sqlite3_file`
对象所代表的打开文件执行各种操作的方法。

如果 `sqlite3_vfs.xOpen` 方法把 `sqlite3_file.pMethods` 元素设为非 NULL 指针，
那么即使 `sqlite3_vfs.xOpen` 报告失败，`sqlite3_io_methods.xClose` 方法也可能被
调用。防止 xOpen 失败后调用 xClose 的唯一办法，是让 `sqlite3_vfs.xOpen` 把
`sqlite3_file.pMethods` 元素设为 NULL。

xSync 的 flags 参数可以是 `SQLITE_SYNC_NORMAL` 或 `SQLITE_SYNC_FULL` 之一。
前者是普通的 fsync()；后者是 Mac OS X 风格的 fullsync。`SQLITE_SYNC_DATAONLY`
标志可以 OR 进去，表示只需同步文件的数据而不必同步其 inode。

传给 xLock() 和 xUnlock() 的整数值是以下之一：

- `SQLITE_LOCK_NONE`
- `SQLITE_LOCK_SHARED`
- `SQLITE_LOCK_RESERVED`
- `SQLITE_LOCK_PENDING`
- `SQLITE_LOCK_EXCLUSIVE`

xLock() 提升数据库文件锁，即把锁从 NONE 向 EXCLUSIVE 方向移动。传给 xLock() 的
参数总是 SHARED、RESERVED、PENDING 或 EXCLUSIVE 之一，绝不含 `SQLITE_LOCK_NONE`。
如果数据库文件锁已经处于等于或高于请求锁的状态，则 xLock() 调用为空操作。
xUnlock() 把数据库文件锁降级为 SHARED 或 NONE 之一；如果锁已经处于等于或低于
请求锁的状态，则 xUnlock() 调用为空操作。

xCheckReservedLock() 方法检查是否有某个数据库连接（无论在本进程还是其它进程）
对文件持有 RESERVED、PENDING 或 EXCLUSIVE 锁。它通过其输出指针参数返回：若存在
这样的锁则返回真，否则返回假。

xFileControl() 方法是一个通用接口，允许自定义 VFS 实现通过
`sqlite3_file_control()` 接口直接控制打开的文件。第二个 "op" 参数是一个整数
操作码；第三个参数是一个通用指针，用于指向一个可能容纳参数、或容纳写返回值空间
的结构。xFileControl() 的潜在用途包括：启用带超时的阻塞锁、改变锁定策略（例如
改用 dot-file 锁）、查询锁的状态、或解除过期的锁。SQLite 核心保留所有小于 100
的操作码供自己使用，小于 100 的操作码清单可查阅。定义自定义 xFileControl 方法的
应用应使用大于 100 的操作码以避免冲突。VFS 实现对于不认识的文件控制操作码应返回
`SQLITE_NOTFOUND`。

xSectorSize() 方法返回文件底层设备的扇区大小。扇区大小是能够写入而不会干扰文件
中其它字节的最小写入单位。xDeviceCharacteristics() 方法返回描述底层设备行为的
位向量：

- `SQLITE_IOCAP_ATOMIC`
- `SQLITE_IOCAP_ATOMIC512`
- `SQLITE_IOCAP_ATOMIC1K`
- `SQLITE_IOCAP_ATOMIC2K`
- `SQLITE_IOCAP_ATOMIC4K`
- `SQLITE_IOCAP_ATOMIC8K`
- `SQLITE_IOCAP_ATOMIC16K`
- `SQLITE_IOCAP_ATOMIC32K`
- `SQLITE_IOCAP_ATOMIC64K`
- `SQLITE_IOCAP_SAFE_APPEND`
- `SQLITE_IOCAP_SEQUENTIAL`
- `SQLITE_IOCAP_UNDELETABLE_WHEN_OPEN`
- `SQLITE_IOCAP_POWERSAFE_OVERWRITE`
- `SQLITE_IOCAP_IMMUTABLE`
- `SQLITE_IOCAP_BATCH_ATOMIC`
- `SQLITE_IOCAP_SUBPAGE_READ`

`SQLITE_IOCAP_ATOMIC` 特性表示任意大小的写操作都是原子的。
`SQLITE_IOCAP_ATOMICnnn` 值表示：大小为 nnn 字节、且对齐到 nnn 整数倍地址的
整块写操作是原子的。`SQLITE_IOCAP_SAFE_APPEND` 值表示：向文件追加数据时，先
追加数据、再扩展文件大小，绝不会反过来。`SQLITE_IOCAP_SEQUENTIAL` 特性表示
信息按 xWrite() 调用的顺序写入磁盘。

如果 xRead() 返回 `SQLITE_IOERR_SHORT_READ`，它还必须把缓冲区未读到的部分填零。
未对短读做零填充的 VFS 可能看起来工作正常，但最终会导致数据库损坏。

## 内存分配例程（Memory Allocation Routines）

```
typedef struct sqlite3_mem_methods sqlite3_mem_methods;
struct sqlite3_mem_methods {
  void *(*xMalloc)(int);         /* 内存分配函数 */
  void (*xFree)(void*);          /* 释放先前分配的内存 */
  void *(*xRealloc)(void*,int);  /* 调整内存分配大小 */
  int (*xSize)(void*);           /* 返回一次分配的大小 */
  int (*xRoundup)(int);          /* 把请求大小向上取整到分配大小 */
  int (*xInit)(void*);           /* 初始化内存分配器 */
  void (*xShutdown)(void*);      /* 反初始化内存分配器 */
  void *pAppData;                /* 传给 xInit() 和 xShutdown() 的参数 */
};
```

本对象的一个实例定义 SQLite 与底层内存分配例程之间的接口。

本对象在 SQLite 接口中只在一处使用。当配置选项为 `SQLITE_CONFIG_MALLOC` 或
`SQLITE_CONFIG_GETMALLOC` 时，指向本对象实例的指针是 `sqlite3_config()` 的参数。
通过创建本对象的一个实例、并在配置期间把它传给
`sqlite3_config(SQLITE_CONFIG_MALLOC)`，应用可以为 SQLite 指定一个替代的内存
分配子系统，供其全部动态内存需求使用。

> 注：SQLite 自带多个内建内存分配器，对绝大多数应用来说完全够用；本对象只对
> 少数有特殊内存分配需求的应用有用。本对象也用于 SQLite 测试：指定一个模拟
> 内存耗尽条件的替代分配器，以验证 SQLite 能否从这类条件中优雅恢复。

xMalloc、xRealloc 和 xFree 方法必须像 C 标准库的 malloc()、realloc() 和 free()
函数那样工作。SQLite 保证传给 xRealloc 的第二个参数，总是先前某次 xRoundup
调用返回的值。

xSize 应返回先前从 xMalloc 或 xRealloc 获得的一次内存分配的已分配大小。已分配
大小总是至少与请求大小一样大，但可能更大。

xRoundup 方法返回给定请求大小所对应的已分配大小。大多数内存分配器至少把分配向
上取整到 8 的倍数；有些取整到更大的倍数或 2 的幂。所有经 `sqlite3_malloc()` 或
`sqlite3_realloc()` 进来的内存分配请求都会先调用 xRoundup。如果 xRoundup 返回 0，
将导致对应的内存分配失败。

xInit 方法初始化内存分配器，例如可能分配所需的互斥锁或初始化内部数据结构。
xShutdown 方法由 `sqlite3_shutdown()`（间接地）调用，应释放 xInit 获得的全部资源。
pAppData 指针用作传给 xInit 和 xShutdown 的唯一参数。

SQLite 在调用 xInit 方法时持有 `SQLITE_MUTEX_STATIC_MAIN` 互斥锁，因此 xInit
方法无需是线程安全的。xShutdown 方法只从 `sqlite3_shutdown()` 调用，因此也无需
是线程安全的。对于其余所有方法，只要 `SQLITE_CONFIG_MEMSTATUS` 配置选项处于开启
状态（默认即如此），SQLite 就持有 `SQLITE_MUTEX_STATIC_MEM` 互斥锁，这些方法
因此被自动串行化。但如果禁用了 `SQLITE_CONFIG_MEMSTATUS`，则其余方法必须是
线程安全的，或者自行安排串行化。

SQLite 绝不会在没有间隔的 xShutdown() 调用的情况下多次调用 xInit()。

## 互斥锁句柄（Mutex Handle）

```
typedef struct sqlite3_mutex sqlite3_mutex;
```

SQLite 内部的互斥锁模块把 `sqlite3_mutex` 定义为互斥锁对象的抽象类型。SQLite
核心从不查看 `sqlite3_mutex` 的内部表示，只处理指向该对象的指针。

互斥锁用 `sqlite3_mutex_alloc()` 创建。

## 互斥锁方法对象（Mutex Methods Object）

```
typedef struct sqlite3_mutex_methods sqlite3_mutex_methods;
struct sqlite3_mutex_methods {
  int (*xMutexInit)(void);
  int (*xMutexEnd)(void);
  sqlite3_mutex *(*xMutexAlloc)(int);
  void (*xMutexFree)(sqlite3_mutex *);
  void (*xMutexEnter)(sqlite3_mutex *);
  int (*xMutexTry)(sqlite3_mutex *);
  void (*xMutexLeave)(sqlite3_mutex *);
  int (*xMutexHeld)(sqlite3_mutex *);

## 互斥锁方法对象（续）

  int (*xMutexNotheld)(sqlite3_mutex *);
};

```

本结构的一个实例定义用于分配和使用互斥锁的底层例程。

通常，SQLite 提供的默认互斥锁实现已经足够；但对某些 SQLite 未提供合适实现的
专用部署或系统，应用可以选择替换为自定义实现。此时，应用创建并填充本结构的一个
实例，连同 `SQLITE_CONFIG_MUTEX` 选项一起传给 `sqlite3_config()`。此外，在使用
`SQLITE_CONFIG_GETMUTEX` 选项查询系统当前的互斥锁实现时，本结构的一个实例可
用作输出变量。

本结构定义的 xMutexInit 方法，由 `sqlite3_initialize()` 函数在系统初始化时调用。
每次有效的 `sqlite3_initialize()` 调用，SQLite 恰好调用一次 xMutexInit 例程。

本结构定义的 xMutexEnd 方法，由 `sqlite3_shutdown()` 函数在系统关闭时调用。
该方法的实现应释放互斥锁方法实现获得的全部未释放资源，尤其是 xMutexInit 方法
获得的资源。每次 `sqlite3_shutdown()` 调用，xMutexEnd() 接口恰好被调用一次。

本结构定义的其余七个方法（xMutexAlloc、xMutexFree、xMutexEnter、xMutexTry、
xMutexLeave、xMutexHeld 和 xMutexNotheld）分别实现以下接口：

- sqlite3_mutex_alloc()
- sqlite3_mutex_free()
- sqlite3_mutex_enter()
- sqlite3_mutex_try()
- sqlite3_mutex_leave()
- sqlite3_mutex_held()
- sqlite3_mutex_notheld()

唯一的区别是：上述公开的 sqlite3_XXX 函数会静默忽略传 NULL 指针（而非有效
互斥锁句柄）的调用；而本结构定义的方法实现并不要求处理这种情况。传 NULL 指针
而非有效互斥锁句柄的结果未定义（即允许实现为：被传入 NULL 指针时发生段错误）。

xMutexInit() 方法必须是线程安全的；在同一个进程内、且没有间隔的 xMutexEnd() 调用
的情况下多次调用 xMutexInit() 必须是无害的。对 xMutexInit() 的第二次及后续调用
必须为空操作。

xMutexInit() 不得使用 SQLite 内存分配（`sqlite3_malloc()` 及其相关函数）。
类似地，xMutexAlloc() 为静态互斥锁不得使用 SQLite 内存分配；但 xMutexAlloc()
可以为快速互斥锁或递归互斥锁使用 SQLite 内存分配。

SQLite 会在调用 `sqlite3_shutdown()` 时调用 xMutexEnd() 方法，但前提是先前对
xMutexInit 的调用返回了 `SQLITE_OK`。如果 xMutexInit 以任何方式失败，应在返回
之前自行清理。

## 自定义页缓存对象（Custom Page Cache Object）

```
typedef struct sqlite3_pcache sqlite3_pcache;
```

`sqlite3_pcache` 类型是不透明的。它由可插拔模块实现。SQLite 核心不关心其大小
或内部结构，除持有并传递指向该对象的指针外，从不与 `sqlite3_pcache` 对象打交道。

更多信息见 sqlite3_pcache_methods2。

## 自定义页缓存页对象（Custom Page Cache Object）

```
typedef struct sqlite3_pcache_page sqlite3_pcache_page;
struct sqlite3_pcache_page {
  void *pBuf;        /* 页的内容 */
  void *pExtra;      /* 与页相关的额外信息 */
};
```

`sqlite3_pcache_page` 对象代表页缓存中的单个页。页缓存会分配本对象的实例。
页缓存的各方法把指向本对象实例的指针用作参数或返回值。

更多信息见 sqlite3_pcache_methods2。

## 存放临时文件的文件夹名（Name Of The Folder Holding Temporary Files）

```
SQLITE_EXTERN char *sqlite3_temp_directory;
```

如果把这个全局变量指向某个文件夹（即目录）名字的字符串，则使用内建 VFS 时
SQLite 创建的所有临时文件都将放在该目录中。如果此变量是 NULL 指针，则 SQLite
会搜索合适的临时文件目录。

强烈不建议应用使用这个全局变量。在 Windows Runtime（WinRT）上必须设置临时
文件夹；但对其它所有平台，强烈建议应用既不要读也不要写此变量。这个全局变量是
为旧应用的向后兼容而存在的遗留物，新项目应避免使用。

在多个线程中同时读取或修改此变量是不安全的；如果另一个线程同时在使用某个数据库
连接，那么读取或修改此变量也是不安全的。此变量的本意是：在进程初始化阶段、
任何 SQLite 接口例程被调用之前设置一次，此后保持不变。

temp_store_directory pragma 可能修改此变量，使其指向由 `sqlite3_malloc` 获得的
内存。此外，temp_store_directory pragma 总是假定此变量指向的字符串存放在由
`sqlite3_malloc` 获得的内存里，并且该 pragma 可能尝试用 `sqlite3_free` 释放那块
内存。因此，如果直接修改此变量，要么把它置为 NULL，要么让它指向由
`sqlite3_malloc` 获得的内存，否则应避免使用 temp_store_directory pragma。
除非 temp_store_directory pragma 要求，否则 SQLite 不会释放 `sqlite3_temp_directory`
指向的内存。如果应用想释放那块内存，必须自行释放，并注意只在所有数据库连接
对象都被销毁之后进行。

> 注：Windows Runtime 用户：必须在调用 `sqlite3_open` 或 `sqlite3_open_v2` 之前
> 设置临时目录，否则需要临时文件的各项功能可能失败。以下是用 C++ 配合 Windows
> Runtime 设置的示例：

```
LPCWSTR zPath = Windows::Storage::ApplicationData::Current->
      TemporaryFolder->Path->Data();
char zPathBuf[MAX_PATH + 1];
memset(zPathBuf, 0, sizeof(zPathBuf));
WideCharToMultiByte(CP_UTF8, 0, zPath, -1, zPathBuf, sizeof(zPathBuf),
      NULL, NULL);
sqlite3_temp_directory = sqlite3_mprintf("%s", zPathBuf);
```

## OS 接口对象（OS Interface Object）

```
typedef struct sqlite3_vfs sqlite3_vfs;
typedef void (*sqlite3_syscall_ptr)(void);
struct sqlite3_vfs {
  int iVersion;            /* 结构版本号（当前为 3） */
  int szOsFile;            /* 子类化 sqlite3_file 的大小 */
  int mxPathname;          /* 最大文件路径名长度 */
  sqlite3_vfs *pNext;      /* 下一个已注册的 VFS */
  const char *zName;       /* 本虚拟文件系统的名字 */
  void *pAppData;          /* 指向应用特定数据的指针 */
  int (*xOpen)(sqlite3_vfs*, sqlite3_filename zName, sqlite3_file*,
               int flags, int *pOutFlags);
  int (*xDelete)(sqlite3_vfs*, const char *zName, int syncDir);
  int (*xAccess)(sqlite3_vfs*, const char *zName, int flags, int *pResOut);
  int (*xFullPathname)(sqlite3_vfs*, const char *zName, int nOut, char *zOut);
  void *(*xDlOpen)(sqlite3_vfs*, const char *zFilename);
  void (*xDlError)(sqlite3_vfs*, int nByte, char *zErrMsg);
  void (*(*xDlSym)(sqlite3_vfs*,void*, const char *zSymbol))(void);
  void (*xDlClose)(sqlite3_vfs*, void*);
  int (*xRandomness)(sqlite3_vfs*, int nByte, char *zOut);
  int (*xSleep)(sqlite3_vfs*, int microseconds);
  int (*xCurrentTime)(sqlite3_vfs*, double*);
  int (*xGetLastError)(sqlite3_vfs*, int, char *);
  /*
  ** 以上方法在 sqlite_vfs 对象定义的第 1 版中。
  ** 其后的方法在第 2 版或更晚版本中新增。
  */
  int (*xCurrentTimeInt64)(sqlite3_vfs*, sqlite3_int64*);
  /*
  ** 以上方法在第 1 版和第 2 版 sqlite_vfs 对象中。
  ** 以下方法针对第 3 版及更高版本。
  */
  int (*xSetSystemCall)(sqlite3_vfs*, const char *zName, sqlite3_syscall_ptr);
  sqlite3_syscall_ptr (*xGetSystemCall)(sqlite3_vfs*, const char *zName);
  const char *(*xNextSystemCall)(sqlite3_vfs*, const char *zName);
  /*
  ** 以上方法在第 1 版到第 3 版 sqlite_vfs 对象中。
  ** 未来版本可能追加新字段；每当此时 iVersion 值会递增。
  */
};
```

`sqlite3_vfs` 对象的一个实例定义 SQLite 核心与底层操作系统之间的接口。对象名字
里的 "vfs" 代表 "virtual file system"（虚拟文件系统）。更多信息参见 VFS 文档。

VFS 接口有时通过在末尾追加新方法来扩展。每次发生这样的扩展，iVersion 字段就会
递增。iVersion 值最初在 SQLite 版本 3.5.0（2007-09-04）时为 1，在 SQLite 版本
3.7.0（2010-07-21）时增为 2，在 SQLite 版本 3.7.6（2011-04-12）时增为 3。未来
版本的 SQLite 可能继续追加字段、并再次递增 iVersion 值。注意：由于疏忽，在
SQLite 版本 3.5.9 过渡到 3.6.0（2008-07-16）时 `sqlite3_vfs` 对象的结构发生了
改变，但 iVersion 字段并未递增。

szOsFile 字段是本 VFS 使用的子类化 `sqlite3_file` 结构的大小。mxPathname 是本
VFS 中路径名的最大长度。

已注册的 `sqlite3_vfs` 对象保存在由 pNext 指针构成的链表中。`sqlite3_vfs_register()`
和 `sqlite3_vfs_unregister()` 接口以线程安全的方式管理此链表。
`sqlite3_vfs_find()` 接口在链表中查找。应用代码和 VFS 实现都不应使用 pNext 指针。

pNext 字段是 `sqlite3_vfs` 结构中 SQLite 会修改的唯一字段。SQLite 只在持有某个
特定静态互斥锁时访问或修改此字段。对象一经注册，应用绝不应修改 `sqlite3_vfs`
对象内的任何内容。

zName 字段保存 VFS 模块的名字，该名字在所有 VFS 模块中必须唯一。

SQLite 保证传给 xOpen 的 zFilename 参数要么是 NULL 指针，要么是由 xFullPathname()
获得、并带可选后缀的字符串。若 zFilename 参数加了后缀，该后缀由一个 "-" 字符后
跟不超过 11 个字母数字和/或 "-" 字符组成。SQLite 还保证该字符串在 xClose() 被
调用前一直有效且不变。由于这一点，`sqlite3_file` 可以在需要记住文件名时安全地
存放指向该文件名的指针。如果传给 xOpen 的 zFilename 参数是 NULL 指针，则 xOpen
必须为该文件自创一个临时名字。每当 xFilename 参数为 NULL 时，flags 参数也必然
包含 `SQLITE_OPEN_DELETEONCLOSE`。

传给 xOpen() 的 flags 参数包含传给 `sqlite3_open_v2()` 的 flags 参数中设置的所有
位。或者，若使用 `sqlite3_open()` 或 `sqlite3_open16()`，则 flags 至少包含
`SQLITE_OPEN_READWRITE | SQLITE_OPEN_CREATE`。若 xOpen() 以只读方式打开文件，
则它把 *pOutFlags 置为包含 `SQLITE_OPEN_READONLY`；*pOutFlags 中可能还设置其它位。

SQLite 还会根据被打开的对象，在 xOpen() 调用中追加以下标志之一：

- `SQLITE_OPEN_MAIN_DB`
- `SQLITE_OPEN_MAIN_JOURNAL`
- `SQLITE_OPEN_TEMP_DB`
- `SQLITE_OPEN_TEMP_JOURNAL`
- `SQLITE_OPEN_TRANSIENT_DB`
- `SQLITE_OPEN_SUBJOURNAL`
- `SQLITE_OPEN_SUPER_JOURNAL`
- `SQLITE_OPEN_WAL`

文件 I/O 实现可以利用对象类型标志来改变它处理文件的方式。例如，不关心崩溃恢复
或回滚的应用可能让日志文件的打开成为空操作；对该日志的写入也成为空操作，而任何
读取日志的尝试都返回 `SQLITE_IOERR`。或者，实现可能识别出数据库文件将做按页对齐
的、随机顺序的扇区读写，并据此设置其 I/O 子系统。

SQLite 还可能把以下标志之一追加到 xOpen 方法：

## xOpen 打开标志（续）

- `SQLITE_OPEN_DELETEONCLOSE`
- `SQLITE_OPEN_EXCLUSIVE`

`SQLITE_OPEN_DELETEONCLOSE` 标志表示文件关闭时应被删除。该标志会为 TEMP 数据库
及其日志、瞬态数据库和子日志设置。

`SQLITE_OPEN_EXCLUSIVE` 标志总是与 `SQLITE_OPEN_CREATE` 标志联用，两者分别直接
对应 POSIX open() API 的 O_EXCL 和 O_CREAT 标志。`SQLITE_OPEN_EXCLUSIVE` 与
`SQLITE_OPEN_CREATE` 配对时，表示文件应总是被创建，且若文件已存在则是错误。
它并不表示文件应以独占访问方式打开。

SQLite 至少分配 szOsFile 字节的内存来容纳作为 xOpen 第三参数传入的
`sqlite3_file` 结构。xOpen 方法不必分配该结构，只需填充它。注意：xOpen 方法
必须把 `sqlite3_file.pMethods` 设为有效的 `sqlite3_io_methods` 对象或 NULL；
即使打开失败也必须如此。SQLite 期望在 xOpen 返回后，无论调用成功与否，
`sqlite3_file.pMethods` 元素都有效。

xAccess() 的 flags 参数可以是 `SQLITE_ACCESS_EXISTS`（测试文件是否存在）、
`SQLITE_ACCESS_READWRITE`（测试文件是否可读可写）、或 `SQLITE_ACCESS_READ`
（测试文件是否至少可读）。`SQLITE_ACCESS_READ` 标志实际上从未被使用，SQLite
的内建 VFS 也未实现它。文件名由第二个参数指定，且可以是目录。xAccess 方法成功
时返回 `SQLITE_OK`；若有 I/O 错误、或第二个参数给定的文件名非法，则返回某个
非零错误码。若返回 `SQLITE_OK`，则向 *pResOut 写入非零或零，指示文件是否可访问。

SQLite 总是为 xFullPathname 的输出缓冲区分配至少 mxPathname+1 字节。输出缓冲区
的确切大小也作为参数传给两个方法。若输出缓冲区不够大，应返回 `SQLITE_CANTOPEN`。
由于 SQLite 会把这视为致命错误，VFS 实现应努力通过把 mxPathname 设得足够大来
避免这种情况。

xRandomness()、xSleep()、xCurrentTime() 和 xCurrentTimeInt64() 接口并非严格属于
文件系统，但为了完整性而包含在 VFS 结构中。xRandomness() 函数尝试把 nBytes 字节
的高质量随机数写入 zOut，返回值是实际获得的随机数字节数。xSleep() 方法使调用
线程至少睡眠给定的微秒数。xCurrentTime() 方法以浮点值返回当前日期时间的儒略日
编号。xCurrentTimeInt64() 方法以整数返回儒略日编号乘以 86400000（24 小时的毫秒
数）。若 xCurrentTimeInt64() 方法可用（iVersion 为 2 或更高、且函数指针非 NULL），
SQLite 将用它获取当前日期时间；若不可用，则回退到 xCurrentTime()。

xSetSystemCall()、xGetSystemCall() 和 xNextSystemCall() 接口不被 SQLite 核心使用。
这些可选接口由某些 VFS 提供，用于方便测试 VFS 代码。通过用自己控制的函数覆盖
系统调用，测试程序可以模拟其它方式难以或无法引发的故障和错误条件。可被覆盖的
系统调用集合因 VFS 而异，也随同一 VFS 的版本而变化。使用这些接口的应用必须
准备好其中任一或全部接口为 NULL，或其行为随版本变化。若 VFS 的 iVersion 小于 3，
应用不得尝试访问这些方法中的任何一个。

## 虚拟表实例对象（Virtual Table Instance Object）

```
struct sqlite3_vtab {
  const sqlite3_module *pModule;  /* 本虚拟表的模块 */
  int nRef;                       /* 打开的游标数 */
  char *zErrMsg;                  /* 来自 sqlite3_mprintf() 的错误消息 */
  /* 虚拟表实现通常会追加额外字段 */
};
```

每个虚拟表模块实现都使用本对象的一个子类来描述虚拟表的特定实例。每个子类都会
针对模块实现的特定需求定制。这个超类的目的是定义所有模块实现共有的字段。

虚拟表方法可以通过把来自 `sqlite3_mprintf()` 的字符串赋给 zErrMsg 来设置错误
消息。方法应注意：在向 zErrMsg 赋新字符串前，先用 `sqlite3_free()` 释放先前
的字符串。错误消息被提交给客户端应用后，字符串会由 `sqlite3_free()` 自动释放，
zErrMsg 字段被清零。

## 获取聚合函数上下文（Obtain Aggregate Function Context）

```
void *sqlite3_aggregate_context(sqlite3_context*, int nBytes);
```

聚合 SQL 函数的实现使用此例程分配内存来存储其状态。

对某个特定的聚合函数，第一次调用 `sqlite3_aggregate_context(C,N)` 例程时，SQLite
分配 N 字节内存、把该内存清零，并返回指向新内存的指针。对同一聚合函数实例，
第二次及后续调用 `sqlite3_aggregate_context()` 时返回同一缓冲区。
`sqlite3_aggregate_context()` 通常在每次调用 xStep 回调时调用一次，并在调用
xFinal 回调时最后一次调用。当聚合查询没有匹配的行时，聚合函数实现的 xStep()
回调从不被调用，而 xFinal() 恰好被调用一次。在这些情况下，
`sqlite3_aggregate_context()` 可能首次从 xFinal() 内部被调用。

当第一次调用时，若 N 小于或等于零、或发生内存分配错误，
`sqlite3_aggregate_context(C,N)` 例程返回 NULL 指针。

`sqlite3_aggregate_context(C,N)` 分配的空间大小由第一次成功调用时的 N 参数决定。
在同一聚合函数实例内后续调用中改变 N 的值不会调整内存分配的大小。在 xFinal
回调内，习惯上在调用 `sqlite3_aggregate_context(C,N)` 时设 N=0，以免发生无意义
的内存分配。

聚合查询结束时，SQLite 自动释放 `sqlite3_aggregate_context()` 分配的内存。

第一个参数必须是实现聚合函数的 xStep 或 xFinal 回调例程的第一参数的副本，即
SQL 函数上下文。

此例程必须在运行该聚合 SQL 函数的同一线程中调用。

## 自动加载静态链接扩展（Automatically Load Statically Linked Extensions）

```
int sqlite3_auto_extension(void(*xEntryPoint)(void));
```

此接口导致 xEntryPoint() 函数在每次创建新数据库连接时被调用。其思想是：
xEntryPoint() 是一个静态链接 SQLite 扩展的入口点，将被自动加载到所有新的
数据库连接中。

尽管函数原型显示 xEntryPoint() 不带参数且返回 void，SQLite 实际以三个参数调用
xEntryPoint()，并期望整数结果，仿佛入口点的签名如下：

```
   int xEntryPoint(
     sqlite3 *db,
     char **pzErrMsg,
     const struct sqlite3_api_routines *pThunk
   );
```

若 xEntryPoint 例程遇到错误，它应让 *pzErrMsg 指向适当的错误消息（从
`sqlite3_mprintf()` 获得）并返回适当的错误码。SQLite 保证在调用 xEntryPoint()
前 *pzErrMsg 为 NULL。xEntryPoint() 返回后，SQLite 会对 *pzErrMsg 调用
`sqlite3_free()`。若任一 xEntryPoint() 返回错误，则诱发该 xEntryPoint() 的
`sqlite3_open()`、`sqlite3_open16()` 或 `sqlite3_open_v2()` 调用将失败。

用已在自动扩展列表上的入口点 X 调用 `sqlite3_auto_extension(X)` 是无害的空操作。
对每个打开的数据库连接，任何入口点都不会被调用超过一次。

另见：sqlite3_reset_auto_extension() 和 sqlite3_cancel_auto_extension()

## Autovacuum 压缩量回调（Autovacuum Compaction Amount Callback）

```
int sqlite3_autovacuum_pages(
  sqlite3 *db,
  unsigned int(*)(void*,const char*,unsigned int,unsigned int,unsigned int),
  void*,
  void(*)(void*)
);
```

`sqlite3_autovacuum_pages(D,C,P,X)` 接口注册一个回调函数 C，在每次对数据库文件
进行 autovacuum 前调用。回调依次收到：通用数据指针 (P) 的副本、正在
被 autovacuum 的附加数据库的 schema 名、数据库文件以页计的大小、空闲页数、以及
每页字节数。回调应返回 autovacuum 应移除的空闲页数。若回调返回零，则不进行
autovacuum。若返回值大于或等于空闲页数，则进行完整的 autovacuum。

若事务提交过程中有多个 ATTACH 的数据库文件被修改，则 autovacuum pages 回调会
对每个文件分别调用。

该回调不可重入。回调函数不应尝试调用任何其它 SQLite 接口；否则可能发生严重
问题，包括段错误和数据库文件损坏。回调函数应是一个简单的、对输入参数做算术
并返回结果的函数。

`sqlite3_autovacuum_pages(D,C,P,X)` 的 X 参数是 P 参数的可选析构函数。若 X 非
NULL，则在数据库连接关闭、或回调被另一次 `sqlite3_autovacuum_pages()` 调用覆盖
时调用 X(P)。

每个数据库连接只有一个 autovacuum pages 回调。每次调用
`sqlite3_autovacuum_pages()` 接口都会覆盖该数据库连接先前所有调用。若传给
`sqlite3_autovacuum_pages(D,C,P,X)` 的回调参数 (C) 是 NULL 指针，则取消
autovacuum steps 回调。`sqlite3_autovacuum_pages()` 的返回值通常是 `SQLITE_OK`，
但也可能是其它错误码。当前实现只返回 `SQLITE_OK` 或 `SQLITE_MISUSE`，但未来
版本可能增加其它返回码。

若未指定 autovacuum pages 回调（通常如此）、或为回调提供 NULL 指针，则默认
行为是压缩所有空闲页。换句话说，默认行为与回调函数形如以下代码相同：

```
    unsigned int demonstration_autovac_pages_callback(
      void *pClientData,
      const char *zSchema,
      unsigned int nDbPage,
      unsigned int nFreePage,
      unsigned int nBytePerPage
    ){
      return nFreePage;
    }
```

## SQL 参数数量（Number Of SQL Parameters）

```
int sqlite3_bind_parameter_count(sqlite3_stmt*);
```

此例程可用于找出预编译语句中的 SQL 参数个数。SQL 参数是形如 "?"、" ?NNN"、
":AAA"、"$AAA" 或 "@AAA" 的记号，作为稍后绑定到参数上的值的占位符。

此例程实际返回最大（最右侧）参数的索引。对于除 ?NNN 外的所有形式，这对应于
唯一参数的个数。若使用了 ?NNN 形式的参数，列表中可能有空隙。

另见：sqlite3_bind()、sqlite3_bind_parameter_name() 和
sqlite3_bind_parameter_index()。

## 按名字查参数索引（Index Of A Parameter With A Given Name）

```
int sqlite3_bind_parameter_index(sqlite3_stmt*, const char *zName);
```

按名字返回 SQL 参数的索引。返回的索引值适合用作 `sqlite3_bind()` 的第二个参数。
若未找到匹配的参数则返回零。即使原语句是用 `sqlite3_prepare16_v2()` 或
`sqlite3_prepare16_v3()` 从 UTF-16 文本准备的，参数名也必须以 UTF-8 给出。

## 宿主参数名（Name Of A Host Parameter）

```
const char *sqlite3_bind_parameter_name(sqlite3_stmt*, int);
```

`sqlite3_bind_parameter_name(P,N)` 接口返回预编译语句 P 中第 N 个 SQL 参数的名字。
形如 "?NNN"、":AAA"、"@AAA" 或 "$AAA" 的 SQL 参数，其名字即为该符号本身所构成的
字符串（含前缀 "?"、":"、"@" 或 "$"）。形如 "?"（后面没有整数）的参数没有名字，
被称为"无名"或"匿名参数"。

第一个宿主参数的索引是 1，而不是 0。

若 N 值越界、或第 N 个参数无名，则返回 NULL。返回的字符串总是 UTF-8 编码，
即使命名参数最初是在 `sqlite3_prepare16()`、`sqlite3_prepare16_v2()` 或
`sqlite3_prepare16_v3()` 中以 UTF-16 指定的。

另见：sqlite3_bind()、sqlite3_bind_parameter_count() 和
sqlite3_bind_parameter_index()。

## 返回打开的 BLOB 的大小（Return The Size Of An Open BLOB）

```
int sqlite3_blob_bytes(sqlite3_blob *);
```

返回其唯一参数中、成功打开的 BLOB 句柄所能访问的 BLOB 的字节大小。增量 BLOB
I/O 例程只能读取或覆盖现有 blob 内容，不能改变 blob 的大小。

此例程只作用于：先前由成功的 `sqlite3_blob_open()` 调用创建、且尚未被
`sqlite3_blob_close()` 关闭的 BLOB 句柄。向此例程传入任何其它指针，结果未定义、
且多半是糟糕的行为。

## 关闭 BLOB 句柄（Close A BLOB Handle）

```
int sqlite3_blob_close(sqlite3_blob *);
```

此函数关闭一个打开的 BLOB 句柄。BLOB 句柄被无条件关闭，即使此例程返回错误码，
句柄仍会被关闭。

若被关闭的 blob 句柄是以读写方式打开的，且数据库处于自动提交模式、没有其它打开
的读写 blob 句柄或活动的写语句，则当前事务被提交。若提交事务时出错，则返回错误
码并把事务回滚。

向此函数传入既非 NULL 指针、又非打开的 blob 句柄的参数，结果未定义。向此例程
传入 NULL 指针（例如失败的 `sqlite3_blob_open()` 调用返回的）是无害的空操作。
否则，若向此函数传入有效的打开 blob 句柄，则在返回前会设置 `sqlite3_errcode()`
和 `sqlite3_errmsg()` 函数返回的值。

## 打开 BLOB 做增量 I/O（Open A BLOB For Incremental I/O）

```
int sqlite3_blob_open(
  sqlite3*,
  const char *zDb,
  const char *zTable,
  const char *zColumn,
  sqlite3_int64 iRow,
  int flags,
  sqlite3_blob **ppBlob
);
```

此接口打开一个句柄，指向数据库 zDb 中、表 zTable、列 zColumn、行 iRow 处的 BLOB；
换句话说，就是下面 SELECT 会选择到的同一个 BLOB：

```
SELECT zColumn FROM zDb.zTable WHERE rowid = iRow;
```

参数 zDb 不是包含数据库的文件名，而是数据库的符号名。对附加数据库，这是 ATTACH
语句中 AS 关键字后面的名字；对主数据库文件，数据库名是 "main"；对 TEMP 表，
数据库名是 "temp"。

若 flags 参数非零，则 BLOB 以读写方式打开；若 flags 参数为零，则 BLOB 以只读
方式打开。

成功时返回 `SQLITE_OK`，新 BLOB 句柄存入 *ppBlob。否则返回错误码；除非错误码是
`SQLITE_MISUSE`，否则 *ppBlob 被置为 NULL。这意味着，只要 API 未被误用，在此
函数返回后对 *ppBlob 调用 `sqlite3_blob_close()` 总是安全的。

若以下任一情况为真，此函数以 `SQLITE_ERROR` 失败：

- 数据库 zDb 不存在
- 表 zTable 在数据库 zDb 中不存在
- 表 zTable 是 WITHOUT ROWID 表
- 列 zColumn 不存在
- 行 iRow 不在表中
- 行 iRow 的指定列包含的值不是 TEXT 或 BLOB 值
- 列 zColumn 是索引、PRIMARY KEY 或 UNIQUE 约束的一部分，且 blob 正以读写方式打开
- 已启用外键约束，且列 zColumn 是子键定义的一部分、blob 正以读写方式打开

除非返回 `SQLITE_MISUSE`，此函数都会设置可通过 `sqlite3_errcode()`、
`sqlite3_errmsg()` 及相关函数访问的数据库连接错误码和消息。

`sqlite3_blob_open()` 引用的 BLOB 可用 `sqlite3_blob_read()` 接口读取、用
`sqlite3_blob_write()` 修改。BLOB 句柄可以用 `sqlite3_blob_reopen()` 接口移到
同一表的另一行。但 BLOB 句柄打开后，其列、表或数据库都不能改变。

若 BLOB 句柄指向的行被 UPDATE、DELETE 或 ON CONFLICT 副作用修改，则 BLOB 句柄
被标记为"过期"。只要该行的任一列被改变（即使不是 BLOB 句柄打开的那一列），
也是如此。对过期 BLOB 句柄调用 `sqlite3_blob_read()` 和 `sqlite3_blob_write()`
会以 `SQLITE_ABORT` 返回码失败。BLOB 过期前写入 BLOB 的更改不会被 BLOB 过期
回滚；若事务继续完成，这些更改最终会提交。

用 `sqlite3_blob_bytes()` 接口确定打开的 blob 的大小。此接口不能改变 blob 的
大小，请用 UPDATE SQL 命令改变 blob 的大小。

`sqlite3_bind_zeroblob()` 和 `sqlite3_result_zeroblob()` 接口以及内建的 zeroblob
SQL 函数，可用于创建用增量 blob 接口读写所需的零填充 blob。

为避免资源泄漏，每个打开的 BLOB 句柄最终都应由对 `sqlite3_blob_close()` 的调用
释放。

另见：sqlite3_blob_close()、sqlite3_blob_reopen()、sqlite3_blob_read()、
sqlite3_blob_bytes()、sqlite3_blob_write()。

## 从 BLOB 增量读取数据（Read Data From A BLOB Incrementally）

```
int sqlite3_blob_read(sqlite3_blob *, void *Z, int N, int iOffset);
```

此函数用于把数据从打开的 BLOB 句柄读入调用者提供的缓冲区。从打开的 BLOB 中、
从偏移 iOffset 开始，把 N 字节数据拷入缓冲区 Z。

若偏移 iOffset 距 BLOB 末尾不足 N 字节，返回 `SQLITE_ERROR` 且不读取数据。
若 N 或 iOffset 小于零，返回 `SQLITE_ERROR` 且不读取数据。blob 的大小（因而
N+iOffset 的最大值）可用 `sqlite3_blob_bytes()` 接口确定。

尝试读取过期 BLOB 句柄会以 `SQLITE_ABORT` 错误码失败。

成功时 `sqlite3_blob_read()` 返回 `SQLITE_OK`，否则返回错误码或扩展错误码。

此例程只作用于：先前由成功的 `sqlite3_blob_open()` 调用创建、且尚未被
`sqlite3_blob_close()` 关闭的 BLOB 句柄。向此例程传入任何其它指针，结果未定义、
且多半是糟糕的行为。

另见：sqlite3_blob_write()。

## 把 BLOB 句柄移到新行（Move a BLOB Handle to a New Row）

```
int sqlite3_blob_reopen(sqlite3_blob *, sqlite3_int64);
```

此函数用于移动现有 BLOB 句柄，使它指向同一数据库表的另一行。新行由第二个参数
传入的 rowid 值标识。只有行可以改变；blob 句柄打开的数据库、表和列保持不变。
把现有 BLOB 句柄移到新行比关闭现有句柄再打开新的更快。

新行必须满足与 `sqlite3_blob_open()` 相同的条件——它必须存在，且指定列中必须
存储有 blob 或 text 值。若新行不在表中、或其中没有 blob 或 text 值、或发生其它
错误，则返回 SQLite 错误码，且该 blob 句柄被视为"中止"。此后对中止的 blob 句柄
调用 `sqlite3_blob_read()`、`sqlite3_blob_write()` 或 `sqlite3_blob_reopen()`
都会立即返回 `SQLITE_ABORT`。对中止的 blob 句柄调用 `sqlite3_blob_bytes()` 总是
返回零。

此函数设置数据库句柄的错误码和消息。

## 向 BLOB 增量写入数据（Write Data Into A BLOB Incrementally）

```
int sqlite3_blob_write(sqlite3_blob *, const void *z, int n, int iOffset);
```

此函数用于把调用者提供的缓冲区中的数据写入打开的 BLOB 句柄。从缓冲区 Z 中、
从偏移 iOffset 开始，把 N 字节数据拷入打开的 BLOB。

成功时 `sqlite3_blob_write()` 返回 `SQLITE_OK`，否则返回错误码或扩展错误码。
除非返回 `SQLITE_MISUSE`，此函数都会设置可通过 `sqlite3_errcode()`、
`sqlite3_errmsg()` 及相关函数访问的数据库连接错误码和消息。

若传入的第一个参数（BLOB 句柄）不是为写入而打开的（传给 `sqlite3_blob_open()`
的 flags 参数为零），此函数返回 `SQLITE_READONLY`。

此函数只能修改 BLOB 的内容，不可能用此 API 增大 BLOB 的大小。若偏移 iOffset 距
BLOB 末尾不足 N 字节，返回 `SQLITE_ERROR` 且不写入数据。blob 的大小（因而
N+iOffset 的最大值）可用 `sqlite3_blob_bytes()` 接口确定。若 N 或 iOffset 小于
零，返回 `SQLITE_ERROR` 且不写入数据。

尝试写入过期 BLOB 句柄会以 `SQLITE_ABORT` 错误码失败。BLOB 句柄过期前对 BLOB
的写入不会被句柄过期回滚，尽管这些更改当然可能已被使 BLOB 句柄过期的语句或其它
独立语句覆盖。

此例程只作用于：先前由成功的 `sqlite3_blob_open()` 调用创建、且尚未被
`sqlite3_blob_close()` 关闭的 BLOB 句柄。向此例程传入任何其它指针，结果未定义、
且多半是糟糕的行为。

另见：sqlite3_blob_read()。

## 设置忙超时（Set A Busy Timeout）

```
int sqlite3_busy_timeout(sqlite3*, int ms);
```

此例程设置一个忙处理器：当表被锁定时，处理器睡眠指定的一段时间。处理器会多次
睡眠，直到累计睡眠至少 "ms" 毫秒。至少睡眠 "ms" 毫秒后，处理器返回 0，导致
`sqlite3_step()` 返回 `SQLITE_BUSY`。

用小于或等于零的参数调用此例程会关闭所有忙处理器。

在任何给定时刻，一个数据库连接只能有一个忙处理器。若在此例程之前已用
`sqlite3_busy_handler()` 定义了另一个忙处理器，则该处理器会被清除。

另见：PRAGMA busy_timeout

## 取消自动扩展加载（Cancel Automatic Extension Loading）

```
int sqlite3_cancel_auto_extension(void(*xEntryPoint)(void));
```

`sqlite3_cancel_auto_extension(X)` 接口取消注册先前用 `sqlite3_auto_extension(X)`
注册的初始化例程 X。若初始化例程 X 被成功取消注册，`sqlite3_cancel_auto_extension(X)`
例程返回 1；若 X 不在初始化例程列表中，则返回 0。

## 重置预编译语句上的所有绑定（Reset All Bindings On A Prepared Statement）

```
int sqlite3_clear_bindings(sqlite3_stmt*);
```

与许多人的直觉相反，`sqlite3_reset()` 不会重置预编译语句上的绑定。请用此例程把
所有宿主参数重置为 NULL。

## 结果集中的列数（Number Of Columns In A Result Set）

```
int sqlite3_column_count(sqlite3_stmt *pStmt);
```

返回预编译语句返回的结果集中的列数。若此例程返回 0，表示该预编译语句不返回
数据（例如 UPDATE）。但是，此例程返回正数并不意味着一行或多行数据会被返回。
SELECT 语句的 `sqlite3_column_count()` 总是正数，但取决于 WHERE 子句约束和表
内容，它可能不返回任何行。

另见：sqlite3_data_count()

## 配置 SQLite 库（Configuring The SQLite Library）

```
int sqlite3_config(int, ...);
```

`sqlite3_config()` 接口用于对 SQLite 做全局配置更改，以把 SQLite 调优到应用
的具体需求。对大多数应用推荐默认配置，因此通常不需要此例程。它用于支持少数
有特殊需求的罕见应用。

`sqlite3_config()` 接口不是线程安全的。应用必须确保在 `sqlite3_config()` 运行
期间，没有其它线程调用其它 SQLite 接口。

`sqlite3_config()` 的第一个参数是一个整数配置选项，决定要配置 SQLite 的哪个
属性。后续参数随第一个参数中的配置选项而变化。

对大多数配置选项，`sqlite3_config()` 接口只能在用 `sqlite3_initialize()` 进行
库初始化之前、或经 `sqlite3_shutdown()` 关闭之后调用。那些可在任何时刻调用的
例外配置选项称为"随时配置选项"。若在 `sqlite3_initialize()` 之后、
`sqlite3_shutdown()` 之前、用非随时配置选项的首参数调用 `sqlite3_config()`，
则该调用返回 `SQLITE_MISUSE`。注意：`sqlite3_config()` 可以在应用自定义的
`sqlite3_os_init()` 的实现中被调用。

设置配置选项时，`sqlite3_config()` 返回 `SQLITE_OK`。若选项未知或 SQLite 无法
设置该选项，则此例程返回非零错误码。

## 函数所属的数据库连接（Database Connection For Functions）

```
sqlite3 *sqlite3_context_db_handle(sqlite3_context*);
```

`sqlite3_context_db_handle()` 接口返回数据库连接指针（第一参数）的副本，即最初
注册该应用自定义函数的 `sqlite3_create_function()` 和
`sqlite3_create_function16()` 例程的第一个参数。

## 结果集当前行的列数（Number of columns in a result set）

```
int sqlite3_data_count(sqlite3_stmt *pStmt);
```

`sqlite3_data_count(P)` 接口返回预编译语句 P 结果集当前行中的列数。若预编译语句
P 没有准备好返回的结果（通过 `sqlite3_column()` 系列接口访问），则
`sqlite3_data_count(P)` 返回 0。若 P 是 NULL 指针，`sqlite3_data_count(P)` 例程
也返回 0。若先前对 `sqlite3_step(P)` 的调用返回 `SQLITE_DONE`，
`sqlite3_data_count(P)` 例程返回 0；若先前调用返回 `SQLITE_ROW`，则返回非零，
但 PRAGMA incremental_vacuum 除外——由于该多步 pragma 的每一步都返回 0 列数据，
它总是返回 0。

另见：sqlite3_column_count()

## 日志对应的数据库文件（Database File Corresponding To A Journal）

```
sqlite3_file *sqlite3_database_file_object(const char*);
```

若 X 是传入 `sqlite3_vfs` 的 xOpen 方法的回滚日志或 WAL 模式日志文件名，则
`sqlite3_database_file_object(X)` 返回代表主数据库文件的 `sqlite3_file` 对象的
指针。

此例程只供自定义 VFS 实现使用，不是通用接口。参数必须是已传入
`sqlite3_vfs.xOpen` 方法的文件名指针，且 xOpen 的 flags 参数包含
`SQLITE_OPEN_MAIN_JOURNAL` 或 `SQLITE_OPEN_WAL` 位之一。任何其它用途的结果
未定义、且多半是糟糕的行为。

## 事务中途把缓存刷到磁盘（Flush caches to disk mid-transaction）

```
int sqlite3_db_cacheflush(sqlite3*);
```

若在数据库连接 D 上打开写事务时调用 `sqlite3_db_cacheflush(D)` 接口，则
pager-cache 中任何当前不在使用中的脏页会被写出到磁盘。若有活动的 SQL 语句创建
的数据库游标正在读取某个脏页，或该脏页是数据库文件的第 1 页（第 1 页总是"使用
中"），则该脏页可能在使用中。`sqlite3_db_cacheflush(D)` 接口刷新所有 schema 的
缓存——"main"、"temp" 以及任何附加数据库。

若此函数在脏页能刷到磁盘前需要取得额外的数据库锁，它会去取。若不能立即取得
这些锁、且配置了忙处理器回调，则按常规方式调用它。若仍无法取得所需锁，则跳过
该数据库，并尝试刷新下一个（如果有）数据库的脏页。若有数据库因无法取得锁而被
跳过、但没有发生其它错误，此函数返回 `SQLITE_BUSY`。

若在把脏页刷到磁盘时发生任何其它错误（例如 I/O 错误或内存不足），则放弃处理、
立即向调用者返回 SQLite 错误码。

否则，若没有错误发生，`sqlite3_db_cacheflush()` 返回 `SQLITE_OK`。

此函数不设置由 `sqlite3_errcode()` 和 `sqlite3_errmsg()` 函数返回的数据库句柄
错误码或消息。

## 配置数据库连接（Configure database connections）

```
int sqlite3_db_config(sqlite3*, int op, ...);
```

`sqlite3_db_config()` 接口用于对数据库连接做配置更改。该接口与
`sqlite3_config()` 类似，只是更改适用于单个数据库连接（由第一个参数指定）。

`sqlite3_db_config(D,V,...)` 的第二个参数是配置动词——一个整数代码，指示正在
配置数据库连接的哪个方面。后续参数随配置动词而变化。

当且仅当调用被认为成功时，对 `sqlite3_db_config()` 的调用返回 `SQLITE_OK`。

## 返回数据库连接的文件名（Return The Filename For A Database Connection）

```
sqlite3_filename sqlite3_db_filename(sqlite3 *db, const char *zDbName);
```

`sqlite3_db_filename(D,N)` 接口返回与连接 D 的数据库 N 相关联的文件名指针。
若数据库连接 D 上没有附加数据库 N，或数据库 N 是临时数据库或内存数据库，则此
函数返回 NULL 指针或空字符串。

此例程返回的字符串值由数据库连接所有和管理。该值在数据库 N 被 DETACH 之前、
或数据库连接关闭之前一直有效。

此函数返回的文件名是 VFS 的 xFullPathname 方法的输出。换句话说，即使最初打开
数据库时用的是 URI 或相对路径名，文件名也将是绝对路径名。

若此例程返回的文件名指针非 NULL，则它可用作以下例程的文件名输入参数：

- sqlite3_uri_parameter()
- sqlite3_uri_boolean()
- sqlite3_uri_int64()
- sqlite3_filename_database()
- sqlite3_filename_journal()
- sqlite3_filename_wal()

## 查找预编译语句的数据库句柄（Find The Database Handle Of A Prepared Statement）

```
sqlite3 *sqlite3_db_handle(sqlite3_stmt*);
```

`sqlite3_db_handle` 接口返回预编译语句所属的数据库连接句柄。返回的数据库连接与
最初创建该语句的 `sqlite3_prepare_v2()` 调用（或其变体）的第一个参数是同一个
数据库连接。

## 取数据库连接的互斥锁（Retrieve the mutex for a database connection）

```
sqlite3_mutex *sqlite3_db_mutex(sqlite3*);
```

当线程模式为 Serialized 时，此接口返回一个指向 `sqlite3_mutex` 对象的指针，
该对象串行化对参数中给定数据库连接的访问。若线程模式为 Single-thread 或
Multi-thread，则此例程返回 NULL 指针。

## 返回数据库连接的 schema 名（Return The Schema Name For A Database Connection）

```
const char *sqlite3_db_name(sqlite3 *db, int N);
```

`sqlite3_db_name(D,N)` 接口返回数据库连接 D 上第 N 个数据库的 schema 名指针；
若 N 越界则返回 NULL 指针。N 值为 0 表示主数据库文件；N 为 1 是 "temp" schema；
更大的 N 值对应各种 ATTACH 的数据库。

`sqlite3_db_name()` 返回的字符串存储空间由 SQLite 自己管理。该字符串可能被任何
改变 schema 的操作释放，包括 ATTACH、DETACH、或对 `sqlite3_serialize()` 或
`sqlite3_deserialize()` 的调用，甚至包括发生在不同线程上的操作。需要长期记住该
字符串的应用应自行复制。在多个线程上同时访问同一数据库连接的应用，应为此 API
的调用做互斥保护，并在释放互斥锁前自行保存结果的私有副本。

## 判断数据库是否只读（Determine if a database is read-only）

```
int sqlite3_db_readonly(sqlite3 *db, const char *zDbName);
```

`sqlite3_db_readonly(D,N)` 接口：若连接 D 的数据库 N 是只读的返回 1，若可读可写
返回 0，若 N 不存在返回 -1。

## 释放数据库连接占用的内存（Free Memory Used By A Database Connection）

```
int sqlite3_db_release_memory(sqlite3*);
```

`sqlite3_db_release_memory(D)` 接口尽量从数据库连接 D 释放尽可能多的堆内存。
与 `sqlite3_release_memory()` 接口不同，即使省略 `SQLITE_ENABLE_MEMORY_MANAGEMENT`
编译期选项，此接口也生效。

另见：sqlite3_release_memory()

## 声明虚拟表的 schema（Declare The Schema Of A Virtual Table）

```
int sqlite3_declare_vtab(sqlite3*, const char *zSQL);
```

虚拟表模块的 xCreate 和 xConnect 方法调用此接口，来声明它们实现的虚拟表的格式
（各列的名字和数据类型）。

## 反序列化数据库（Deserialize a database）

```
int sqlite3_deserialize(
  sqlite3 *db,            /* 数据库连接 */
  const char *zSchema,    /* 用反序列化重新打开的数据库 */
  unsigned char *pData,   /* 序列化后的数据库内容 */
  sqlite3_int64 szDb,     /* 反序列化的字节数 */
  sqlite3_int64 szBuf,    /* 缓冲区 pData[] 的总大小 */
  unsigned mFlags         /* 零个或多个 SQLITE_DESERIALIZE_* 标志 */
);
```

`sqlite3_deserialize(D,S,P,N,M,F)` 接口解除数据库连接 D 与数据库 S 的关联，
然后基于 P 中包含的序列化内容把 S 作为内存数据库重新打开。若 S 是 NULL 指针，
则使用主数据库。序列化后的数据库 P 大小为 N 字节。M 是缓冲区 P 的大小，可能大于
N。若 M 大于 N、且 F 中未设置 `SQLITE_DESERIALIZE_READONLY` 位，则只要总大小
不超过 M 字节，SQLite 就被允许向内存数据库添加内容。

若 F 中设置了 `SQLITE_DESERIALIZE_FREEONCLOSE` 位，则数据库连接关闭时 SQLite
将对序列化缓冲区调用 `sqlite3_free()`。若设置了 `SQLITE_DESERIALIZE_RESIZEABLE`
位，则当对数据库的写入导致其增长到超过 M 字节时，SQLite 会尝试用
`sqlite3_realloc64()` 增大缓冲区大小。

在数据库连接 D 关闭之前，应用不得修改缓冲区 P 或使其失效。

若数据库当前处于读事务中或正参与备份操作，`sqlite3_deserialize()` 接口将以
`SQLITE_BUSY` 失败。

无法反序列化到 TEMP 数据库。若 `sqlite3_deserialize(D,S,P,N,M,F)` 的 S 参数是
"temp"，则函数返回 `SQLITE_ERROR`。

反序列化后的数据库不应处于 WAL 模式。若数据库处于 WAL 模式，则任何使用数据库
文件的尝试都会导致 `SQLITE_CANTOPEN` 错误。应用可以在调用
`sqlite3_deserialize(D,S,P,N,M,F)` 之前，把输入数据库 P 的文件格式版本号
（第 18 和 19 字节）设置为 0x01，从而强制数据库文件进入回滚模式，绕过此限制。

若 `sqlite3_deserialize(D,S,P,N,M,F)` 因任何原因失败、且参数 F 中设置了
`SQLITE_DESERIALIZE_FREEONCLOSE` 位，则在返回前会对参数 P 调用 `sqlite3_free()`。

若用 `SQLITE_OMIT_DESERIALIZE` 选项编译 SQLite，则省略此接口。

## 移除不必要的虚拟表实现（Remove Unnecessary Virtual Table Implementations）

```
int sqlite3_drop_modules(
  sqlite3 *db,                /* 从此连接移除模块 */
  const char **azKeep         /* 但不要移除此处列名的模块 */
);
```

`sqlite3_drop_modules(D,L)` 接口移除数据库连接 D 上除列表 L 中列名外的所有虚拟表
模块。参数 L 必须是 NULL、或一个指向字符串指针数组的指针，该数组由单个 NULL
指针终止。若参数 L 是 NULL，则移除所有虚拟表模块。

另见：sqlite3_create_module()

## 启用或禁用扩展加载（Enable Or Disable Extension Loading）

```
int sqlite3_enable_load_extension(sqlite3 *db, int onoff);
```

为避免在尚未准备好处理扩展加载的旧应用中打开安全漏洞，并作为评估用户输入的
SQL 时禁用扩展加载的手段，提供以下 API 来开关 `sqlite3_load_extension()` 机制。

扩展加载默认关闭。用 onoff==1 调用 `sqlite3_enable_load_extension()` 例程以打开
扩展加载，用 onoff==0 调用以再次关闭。

此接口同时启用或禁用 C-API `sqlite3_load_extension()` 和 SQL 函数
load_extension()。请用 `sqlite3_db_config(db,SQLITE_DBCONFIG_ENABLE_LOAD_EXTENSION,..)`
只启用或禁用 C-API。

> 安全警告：建议用 `SQLITE_DBCONFIG_ENABLE_LOAD_EXTENSION` 方法而非此接口启用
> 扩展加载，使 load_extension() SQL 函数保持禁用。这样可防止 SQL 注入让攻击者
> 获得扩展加载能力。

## 启用或禁用共享页缓存（Enable Or Disable Shared Pager Cache）

```
int sqlite3_enable_shared_cache(int);
```

此例程启用或禁用指向同一数据库的连接之间对数据库缓存和 schema 数据结构的共享。
参数为真则启用共享，为假则禁用。

若用 `-DSQLITE_OMIT_SHARED_CACHE` 编译 SQLite，则省略此接口。推荐使用
`-DSQLITE_OMIT_SHARED_CACHE` 编译期选项，因为不鼓励使用共享缓存模式。

缓存共享对整个进程启用和禁用。这是自 SQLite 版本 3.5.0（2007-09-04）以来的
变化；在更早的 SQLite 版本中，共享是对每个线程分别启用或禁用的。

此接口设置的缓存共享模式影响其后所有对 `sqlite3_open()`、`sqlite3_open_v2()`
和 `sqlite3_open16()` 的调用。现有数据库连接继续使用打开它们时生效的共享模式。

若共享缓存被成功启用或禁用，此例程返回 `SQLITE_OK`，否则返回错误码。

共享缓存默认禁用，建议保持这样。换句话说，不要使用此例程。此接口为历史兼容而
继续提供，但不鼓励使用。任何共享缓存的使用都不被鼓励。若必须使用共享缓存，建议
只用带 `SQLITE_OPEN_SHAREDCACHE` 标志的 `sqlite3_open_v2()` 接口为单个数据库
连接启用。

> 注：此方法在 MacOS X 10.7 和 iOS 版本 5.0 上被禁用，将总是返回 `SQLITE_MISUSE`。
> 在这些系统上，共享缓存模式应通过带 `SQLITE_OPEN_SHAREDCACHE` 的
> `sqlite3_open_v2()` 按数据库连接启用。

此接口在写入 32 位整数是原子的处理器上是线程安全的。

另见：SQLite Shared-Cache Mode

## 一步查询执行接口（One-Step Query Execution Interface）

```
int sqlite3_exec(
  sqlite3*,                                  /* 打开的数据库 */
  const char *sql,                           /* 要求值的 SQL */
  int (*callback)(void*,int,char**,char**),  /* 回调函数 */
  void *,                                    /* 回调的第 1 个参数 */
  char **errmsg                              /* 错误消息写在此处 */
);
```

`sqlite3_exec()` 接口是 `sqlite3_prepare_v2()`、`sqlite3_step()` 和
`sqlite3_finalize()` 的便捷包装，允许应用在不写大量 C 代码的情况下运行多条
SQL 语句。

`sqlite3_exec()` 接口在其第 1 个参数传入的数据库连接上下文中，运行其第 2 个
参数传入的零条或多条 UTF-8 编码、以分号分隔的 SQL 语句。若 `sqlite3_exec()` 的
第 3 个参数（回调函数）非 NULL，则对求值的 SQL 语句产生的每个结果行调用它。
`sqlite3_exec()` 的第 4 个参数被传递到每次回调调用的第 1 个参数。若传给
`sqlite3_exec()` 的回调指针是 NULL，则从不调用回调，结果行被忽略。

若求值传入 `sqlite3_exec()` 的 SQL 语句时发生错误，则当前语句的执行停止，后续
语句被跳过。若 `sqlite3_exec()` 的第 5 个参数非 NULL，则任何错误消息会被写入
从 `sqlite3_malloc()` 获得的内存、并通过第 5 个参数传回。为避免内存泄漏，应用
应在不再需要错误消息字符串后，对 `sqlite3_exec()` 第 5 个参数返回的字符串调用
`sqlite3_free()`。若 `sqlite3_exec()` 的第 5 个参数非 NULL 且没有错误发生，则
`sqlite3_exec()` 在返回前把其第 5 个参数中的指针置为 NULL。

若 `sqlite3_exec()` 回调返回非零，则 `sqlite3_exec()` 例程返回 `SQLITE_ABORT`，
不再调用回调、也不再运行任何后续 SQL 语句。

`sqlite3_exec()` 回调函数的第 2 个参数是结果中的列数。第 3 个参数是一个字符串
指针数组，每个字符串仿佛经 `sqlite3_column_text()` 获得，每个结果列一个。若结果
行的某个元素是 NULL，则回调对应的字符串指针为 NULL 指针。第 4 个参数是一个
字符串指针数组，每项代表对应结果列的名字，仿佛经 `sqlite3_column_name()` 获得。

若 `sqlite3_exec()` 的第 2 个参数是 NULL 指针、空字符串指针、或只包含空白和/或
SQL 注释的指针，则不求值任何 SQL 语句，数据库也不被改变。

限制：

- 应用必须确保 `sqlite3_exec()` 的第 1 个参数是有效且打开的数据库连接。
- 在 `sqlite3_exec()` 运行期间，应用不得关闭其第 1 个参数指定的数据库连接。
- 在 `sqlite3_exec()` 运行期间，应用不得修改传入其第 2 个参数的 SQL 语句文本。
- 返回后，应用不得解引用作为第 3 和第 4 个回调参数传入的数组或字符串指针。

## 启用或禁用扩展结果码（Enable Or Disable Extended Result Codes）

```
int sqlite3_extended_result_codes(sqlite3*, int onoff);
```

`sqlite3_extended_result_codes()` 例程启用或禁用 SQLite 的扩展结果码特性。
为历史兼容，扩展结果码默认禁用。

## 销毁预编译语句对象（Destroy A Prepared Statement Object）

```
int sqlite3_finalize(sqlite3_stmt *pStmt);
```

`sqlite3_finalize()` 函数用于删除预编译语句。若语句最近一次求值未遇到错误、
或语句从未被求值，则 `sqlite3_finalize()` 返回 `SQLITE_OK`。若语句 S 最近一次
求值失败，则 `sqlite3_finalize(S)` 返回适当的错误码或扩展错误码。

`sqlite3_finalize(S)` 例程可以在预编译语句 S 生命周期的任何时刻调用：在语句 S
被求值之前、在一次或多次 `sqlite3_reset()` 调用之后、或任何 `sqlite3_step()`
调用之后（无论语句是否完成执行）。

对 NULL 指针调用 `sqlite3_finalize()` 是无害的空操作。

为避免资源泄漏，应用必须终结每条预编译语句。语句被终结后应用仍尝试使用它是严重
错误。终结后使用预编译语句的任何行为都可能导致未定义、糟糕的行为，如段错误和
堆损坏。

## 最后一次插入的 Rowid（Last Insert Rowid）

```
sqlite3_int64 sqlite3_last_insert_rowid(sqlite3*);
```

大多数 SQLite 表（WITHOUT ROWID 表除外）中的每个条目都有一个唯一的 64 位有符号
整数键，称为 "rowid"。只要 ROWID、OID 或 _ROWID_ 这些名字没有被显式声明的列
占用，rowid 就总是可作为名为 ROWID、OID 或 _ROWID_ 的未声明列使用。若表有 INTEGER
PRIMARY KEY 类型的列，则该列是 rowid 的另一个别名。

`sqlite3_last_insert_rowid(D)` 接口通常返回数据库连接 D 上最近一次对 rowid 表或
虚拟表的成功 INSERT 的 rowid。对 WITHOUT ROWID 表的插入不被记录。若数据库连接 D
上从未发生过对 rowid 表的成功 INSERT，则 `sqlite3_last_insert_rowid(D)` 返回零。

此函数返回的值除了在向数据库表插入行时被自动设置外，还可以由
`sqlite3_set_last_insert_rowid()` 显式设置。

某些虚拟表实现可能在提交事务时向 rowid 表 INSERT 行（例如把累积在内存中的数据
刷到磁盘）。此时，此函数的后续调用会返回与这些内部 INSERT 操作关联的 rowid，
导致不符合直觉的结果。以这种方式向 rowid 表写入的虚拟表实现，可以在把控制权交回
用户前用 `sqlite3_set_last_insert_rowid()` 恢复原始的 rowid 值，从而避免此问题。

若 INSERT 发生在触发器内，则只要触发器正在运行，此例程就返回被插入行的 rowid。
一旦触发器程序结束，此例程返回的值会恢复到触发器被触发前的值。

因约束冲突而失败的 INSERT 不是成功的 INSERT，不会改变此例程返回的值。因此
INSERT OR FAIL、INSERT OR IGNORE、INSERT OR ROLLBACK 和 INSERT OR ABORT 在插入
失败时不改变此例程的返回值。当 INSERT OR REPLACE 遇到约束冲突时，它不会失败；
在删除引起约束问题的行后 INSERT 继续完成，所以 INSERT OR REPLACE 总会改变此
接口的返回值。

就本例程而言，INSERT 即使随后被回滚，也被视为成功。

此函数可通过 last_insert_rowid() SQL 函数供 SQL 语句访问。

若在 `sqlite3_last_insert_rowid()` 函数运行期间，另一个线程对同一数据库连接执行
新的 INSERT、从而改变 last insert rowid，则 `sqlite3_last_insert_rowid()` 返回的
值不可预测，可能既不等于旧值、也不等于新值。

## 运行时限制（Run-time Limits）

```
int sqlite3_limit(sqlite3*, int id, int newVal);
```

此接口允许按连接逐个限制各种构造的大小。第一个参数是待设置或查询限制的数据库
连接。第二个参数是限制类别之一，定义一类要被限制大小的构造。第三个参数是该构造
的新限制。

若新限制是负数，则限制不变。对每个限制类别 SQLITE_LIMIT_NAME，都有一个由名为
SQLITE_MAX_NAME 的 C 预处理宏在编译期设置的硬上限。（名字里的 "_LIMIT_" 会被
改成 "_MAX_"。）试图把限制增大到硬上限之上的行为会被静默截断到硬上限。

无论限制是否改变，`sqlite3_limit()` 接口都返回该限制的先前值。因此，想在不改变
限制的情况下查出当前值，只需用第三个参数 -1 调用此接口。

运行时限制用于既管理自己的内部数据库、也管理由不可信外部源控制的数据库的应用。
一个示例应用是：既有存储历史的自有数据库、又有下载自互联网的 JavaScript 应用
控制的独立数据库的 Web 浏览器。内部数据库可以有大的默认限制；由外部源管理的
数据库可以被赋予小得多的限制，以防止拒绝服务攻击。开发者可能还想用
`sqlite3_set_authorizer()` 接口进一步控制不可信的 SQL。不可信脚本创建的数据库的
大小可用 max_page_count PRAGMA 来限制。

未来版本可能增加新的运行时限制类别。

## 加载扩展（Load An Extension）

```
int sqlite3_load_extension(
  sqlite3 *db,          /* 把扩展加载到此数据库连接 */
  const char *zFile,    /* 包含扩展的共享库文件名 */
  const char *zProc,    /* 入口点。若为 0 则从 zFile 推导 */
  char **pzErrMsg       /* 若非 0，错误消息写在此处 */
);
```

此接口从指定文件加载 SQLite 扩展库。

`sqlite3_load_extension()` 接口尝试加载文件 zFile 中包含的 SQLite 扩展库。若文件
无法直接加载，则尝试加各种操作系统特定的文件扩展名后再加载。例如，若 "samplelib"
无法加载，则可能也会尝试 "samplelib.so"、"samplelib.dylib" 或 "samplelib.dll"
等名字。

入口点是 zProc。zProc 可以是 0，此时 SQLite 会尝试自行得出入口点名字。它先试
"sqlite3_extension_init"。若不行，则试形如 "sqlite3_X_init" 的名字，其中 X 由
文件名中从最后一个 "/" 到后面第一个 "." 之间的所有 ASCII 字母、或所有 ASCII
字母数字字符的小写等价形式组成，并去掉开头的 "lib"。
`sqlite3_load_extension()` 接口成功时返回 `SQLITE_OK`，出错时返回 `SQLITE_ERROR`。
若发生错误且 pzErrMsg 非 0，则 `sqlite3_load_extension()` 接口会尝试用存储在从
`sqlite3_malloc()` 获得的内存中的错误消息文本填充 *pzErrMsg。调用函数应通过调用
`sqlite3_free()` 释放这块内存。

调用此 API 之前必须用 `sqlite3_enable_load_extension()` 或
`sqlite3_db_config(db,SQLITE_DBCONFIG_ENABLE_LOAD_EXTENSION,1,NULL)` 启用扩展
加载，否则将返回错误。

> 安全警告：建议用 `SQLITE_DBCONFIG_ENABLE_LOAD_EXTENSION` 方法只启用此接口。
> 应避免使用 `sqlite3_enable_load_extension()` 接口。这样可使 SQL 函数
> load_extension() 保持禁用，防止 SQL 注入让攻击者获得扩展加载能力。

另见 load_extension() SQL 函数。

## 错误日志接口（Error Logging Interface）

```
void sqlite3_log(int iErrCode, const char *zFormat, ...);
```

`sqlite3_log()` 接口把一条消息写入由 `sqlite3_config()` 的 `SQLITE_CONFIG_LOG`
选项建立的错误日志。若日志已启用，则用 zFormat 字符串及其后续参数配合
`sqlite3_snprintf()` 生成最终输出字符串。

`sqlite3_log()` 接口供虚拟表、排序函数、SQL 函数等扩展使用。虽然没有什么阻止
应用调用 `sqlite3_log()`，但这样做被视为不好的形式。

zFormat 字符串必须非 NULL。

为避免死锁和其它线程问题，`sqlite3_log()` 例程不使用动态分配的内存。日志消息
存储在为固定长度的栈缓冲区中。若日志消息超过几百个字符，将被截断到缓冲区的
长度。

## 查找下一条预编译语句（Find the next prepared statement）

```
sqlite3_stmt *sqlite3_next_stmt(sqlite3 *pDb, sqlite3_stmt *pStmt);
```

此接口返回与数据库连接 pDb 关联的、pStmt 之后的下一条预编译语句指针。若 pStmt
是 NULL，则此接口返回与数据库连接 pDb 关联的第一条预编译语句指针。若没有预编译
语句满足本例程的条件，则返回 NULL。

`sqlite3_next_stmt(D,S)` 调用中的数据库连接指针 D 必须指向打开的数据库连接，
尤其不能是 NULL 指针。

## 为虚拟表重载函数（Overload A Function For A Virtual Table）

```
int sqlite3_overload_function(sqlite3*, const char *zFuncName, int nArg);
```

虚拟表可以用虚拟表模块的 xFindFunction 方法提供函数的替代实现。但要被重载，
这些函数的全局版本必须已存在。

此 API 确保具有特定名字和参数个数的函数全局版本存在。若调用此 API 前没有这样的
函数，则创建一个新函数。新函数的实现总是抛出异常。因此新函数本身没什么用，其
唯一用途是作为可被虚拟表重载的占位函数。

## 查询进度回调（Query Progress Callbacks）

```
void sqlite3_progress_handler(sqlite3*, int, int(*)(void*), void*);
```

`sqlite3_progress_handler(D,N,X,P)` 接口使回调函数 X 在数据库连接 D 上
`sqlite3_step()` 和 `sqlite3_prepare()` 等长时间运行的调用期间被定期调用。此接口
的一个示例用途是在大查询期间保持 GUI 更新。

参数 P 作为唯一参数传给回调函数 X。参数 N 是两次连续回调 X 调用之间求值的虚拟
机指令的大致数量。若 N 小于 1，则禁用进度处理器。

每个数据库连接一次只能定义一个进度处理器；设置新的进度处理器会取消旧的。把参数
X 设为 NULL 会禁用进度处理器。把 N 设为小于 1 的值也会禁用进度处理器。

若进度回调返回非零，则操作被中断。此特性可用于实现 GUI 进度对话框上的"取消"
按钮。

进度处理器回调不得做任何会修改调用它的数据库连接的事情。就本段中的"修改"而言，
注意 `sqlite3_prepare_v2()` 和 `sqlite3_step()` 都会修改它们的数据库连接。

进度处理器回调最初只会从字节码引擎调用。它仍可能在 `sqlite3_prepare()` 等期间
被调用，因为这些例程可能强制重新解析 schema，其中涉及运行字节码引擎。不过从
SQLite 版本 3.41.0 开始，进度处理器回调在分析和生成复杂查询的代码时，也可能被
直接从 `sqlite3_prepare()` 调用。

## 伪随机数生成器（Pseudo-Random Number Generator）

```
void sqlite3_randomness(int N, void *P);
```

SQLite 包含一个高质量的伪随机数生成器（PRNG），用于在向已经使用最大可能 ROWID
的表插入新记录时选择随机 ROWID。该 PRNG 也用于内建的 random() 和 randomblob()
SQL 函数。此接口允许应用为其它用途访问同一个 PRNG。

调用此例程会把 N 字节的随机数存入缓冲区 P。参数 P 可以是 NULL 指针。

若此例程先前未被调用、或前一次调用 N 小于 1 或 P 是 NULL 指针，则 PRNG 用从
默认 `sqlite3_vfs` 对象的 xRandomness 方法获得的随机性播种。若前一次调用此例程
的 N 为 1 或更大、且 P 非 NULL，则伪随机性在内部生成，不求助于 sqlite3_vfs 的
xRandomness 方法。

## 尝试释放堆内存（Attempt To Free Heap Memory）

```

int sqlite3_release_memory(int);

```

`sqlite3_release_memory()` 接口尝试通过释放 SQLite 库持有的非必要内存分配，来释放
N 字节的堆内存。用于缓存数据库页以提高性能的内存是非必要内存的一个例子。
`sqlite3_release_memory()` 返回实际释放的字节数，可能多于或少于请求量。
若 SQLite 未用 `SQLITE_ENABLE_MEMORY_MANAGEMENT` 编译，则 `sqlite3_release_memory()`
例程是返回零的空操作。

另见：sqlite3_db_release_memory()

## 重置预编译语句对象（Reset A Prepared Statement Object）

```
int sqlite3_reset(sqlite3_stmt *pStmt);
```

`sqlite3_reset()` 函数用于把预编译语句对象重置回其初始状态，准备重新执行。
任何经 `sqlite3_bind_*()` API 绑定过值的 SQL 语句参数都保留其值。请用
`sqlite3_clear_bindings()` 重置绑定。

`sqlite3_reset(S)` 接口把预编译语句 S 重置回其程序的起点。

`sqlite3_reset(S)` 的返回码指示预编译语句 S 先前的求值是否成功完成。若 S 上
从未调用过 `sqlite3_step(S)`、或自上次 `sqlite3_reset(S)` 调用以来未再调用过
`sqlite3_step(S)`，则 `sqlite3_reset(S)` 返回 `SQLITE_OK`。

若预编译语句 S 最近一次 `sqlite3_step(S)` 调用指示错误，则 `sqlite3_reset(S)`
返回适当的错误码。即使先前没有错误，但重置预编译语句的过程引起新错误，
`sqlite3_reset(S)` 接口也可能返回错误码。例如，若带 RETURNING 子句的 INSERT 语句
只调用了一次 `sqlite3_step(S)`，那次调用可能返回 `SQLITE_ROW`，但整个语句仍可能
失败；若锁定约束阻止数据库更改提交，`sqlite3_reset(S)` 调用可能返回 `SQLITE_BUSY`。
因此，即使先前没有 `sqlite3_step(S)` 调用指示问题，应用检查 `sqlite3_reset(S)`
的返回码也很重要。

`sqlite3_reset(S)` 接口不改变预编译语句 S 上任何绑定的值。

## 重置自动扩展加载（Reset Automatic Extension Loading）

```
void sqlite3_reset_auto_extension(void);
```

此接口禁用先前用 `sqlite3_auto_extension()` 注册的所有自动扩展。

## 设置 SQL 函数的子类型（Setting The Subtype Of An SQL Function）

```
void sqlite3_result_subtype(sqlite3_context*,unsigned int);
```

`sqlite3_result_subtype(C,T)` 函数使应用自定义 SQL 函数（其 sqlite3_context 为 C）
的结果的子类型成为值 T。在当前版本的 SQLite 中，只保留子类型 T 的低 8 位，高位
被丢弃。SQLite 未来版本可能增加保留的子类型字节数。

每个调用此接口的应用自定义 SQL 函数，在注册该 SQL 函数时都应在文本编码参数中
包含 `SQLITE_RESULT_SUBTYPE` 属性。若调用 `sqlite3_result_subtype()` 的函数省略了
`SQLITE_RESULT_SUBTYPE` 属性，则某些情况下 `sqlite3_result_subtype()` 可能无法
设置结果子类型。

若用 `-DSQLITE_STRICT_SUBTYPE=1` 编译 SQLite，则任何调用
`sqlite3_result_subtype()` 接口、且没有 `SQLITE_RESULT_SUBTYPE` 属性的 SQL 函数
都会报错。未来版本的 SQLite 可能默认启用 `-DSQLITE_STRICT_SUBTYPE=1`。

## 序列化数据库（Serialize a database）

```
unsigned char *sqlite3_serialize(
  sqlite3 *db,           /* 数据库连接 */
  const char *zSchema,   /* 要序列化的数据库。例如："main"、"temp" ... */
  sqlite3_int64 *piSize, /* 若非 NULL，把数据库大小写在此处 */
  unsigned int mFlags    /* 零个或多个 SQLITE_SERIALIZE_* 标志 */
);
```

`sqlite3_serialize(D,S,P,F)` 接口返回指向一段内存的指针，该内存是数据库连接 D 上
数据库 S 的序列化。若 S 是 NULL 指针，则使用主数据库。若 P 非 NULL 指针，则把
数据库的字节大小写入 *P。

对普通的磁盘数据库文件，序列化只是磁盘文件的副本。对内存数据库或 "TEMP" 数据库，
序列化与若把该数据库备份到磁盘会写入的字节序列相同。

通常，`sqlite3_serialize()` 把数据库的序列化复制到从 `sqlite3_malloc64()` 获得的
内存中，并返回指向该内存的指针。调用者负责释放返回的值以避免内存泄漏。但若 F 参数
包含 `SQLITE_SERIALIZE_NOCOPY` 位，则不进行任何内存分配，`sqlite3_serialize()`
函数将返回 SQLite 当前为该数据库使用的连续内存表示的指针；若不存在这样的连续
内存表示，则返回 NULL。数据库的连续内存表示通常只在先前以相同的 D 和 S 值调用过
`sqlite3_deserialize(D,S,...)` 时才存在。即使设置了 `SQLITE_SERIALIZE_NOCOPY` 位、
但数据库没有连续副本，数据库的大小也会写入 *P。

调用之后，若已设置 `SQLITE_SERIALIZE_NOCOPY` 位，则返回的缓冲区内容在连接上的
下一次写操作之前、或连接关闭之前保持可访问且不变，应用不得修改该缓冲区。若该位
已被清除，则调用后 SQLite 不会访问返回的缓冲区。

即使从参数 F 中省略 `SQLITE_SERIALIZE_NOCOPY` 位，若发生内存分配错误，
`sqlite3_serialize(D,S,P,F)` 调用也可能返回 NULL。

若用 `SQLITE_OMIT_DESERIALIZE` 选项编译 SQLite，则省略此接口。

## 设置错误码和消息（Set Error Code And Message）

```
int sqlite3_set_errmsg(sqlite3 *db, int errcode, const char *zErrMsg);
```

把第一个参数传入的数据库句柄的错误码设置为 errcode，把错误消息设置为 NUL 结尾
字符串 zErrMsg 的副本。若 zErrMsg 传 NULL，则错误消息被设置为与所给错误码关联的
默认消息。其后对 `sqlite3_errcode()`、`sqlite3_errmsg()` 等的调用将返回此例程
设置的值，以取代 SQLite 自己先前设置的值。

若错误码和错误消息被成功设置，此函数返回 `SQLITE_OK`；若发生内存不足则返回
`SQLITE_NOMEM`；若数据库句柄为 NULL 或无效则返回 `SQLITE_MISUSE`。

此例程设置的错误码和消息一直有效，直到它们被改变——要么被对它的另一次调用，
要么被 SQLite 自己改变以反映后续某次 API 调用的结果。

此函数供 SQLite 扩展或包装器使用。其思想是：扩展或包装器可以用此例程设置错误
消息和错误码，从而在应用看来更像核心 SQLite 特性。

## 设置最后一次插入的 Rowid 值（Set the Last Insert Rowid value）

```
void sqlite3_set_last_insert_rowid(sqlite3*,sqlite3_int64);
```

`sqlite3_set_last_insert_rowid(D, R)` 方法允许应用把调用
`sqlite3_last_insert_rowid(D)` 返回的值设置为 R，而不向数据库插入行。

## 设置 Setlk 超时（Set the Setlk Timeout）

```
int sqlite3_setlk_timeout(sqlite3*, int ms, int flags);
```

此例程只在 `SQLITE_ENABLE_SETLK_TIMEOUT` 构建中有用。若 VFS 支持阻塞锁，它设置
指定数据库句柄在 wal 模式数据库上取得的符合条件的锁所使用的超时（毫秒）。在非
`SQLITE_ENABLE_SETLK_TIMEOUT` 构建中、或 VFS 不支持阻塞锁时，此函数是空操作。

向此函数传 0 会完全禁用阻塞锁。向此函数传 -1 是请求 VFS 长时间阻塞——若可能则
无限期。传任何其它负值的结果未定义。

每个 SQLite 数据库句柄在内部存储两个超时值：busy-timeout（用于回滚模式数据库，
或 VFS 不支持阻塞锁时）和 setlk-timeout（用于 wal 模式数据库上的阻塞锁）。
`sqlite3_busy_timeout()` 方法同时设置两个值，此函数只设置 setlk-timeout 值。
因此，要为单个数据库句柄配置不同的 busy-timeout 和 setlk-timeout 值，先调用
`sqlite3_busy_timeout()`，再调用此函数。

每当 wal 模式数据库的连接数从 1 降到 0 时，最后一个连接对数据库取排他锁，然后
检查点并删除 wal 文件。在其执行期间，任何试图读取该数据库的新连接都会以
`SQLITE_BUSY` 错误失败。或者，若向此 API 传 `SQLITE_SETLK_BLOCK_ON_CONNECT` 标志，
则新连接阻塞，直到排他锁被释放。

## 短时挂起执行（Suspend Execution For A Short Time）

```
int sqlite3_sleep(int);
```

`sqlite3_sleep()` 函数使当前线程至少挂起执行其参数指定的毫秒数。

若操作系统不支持毫秒级时间分辨率的睡眠请求，则时间向上取整到最近的秒。返回实际
向操作系统请求的睡眠毫秒数。

SQLite 通过调用默认 `sqlite3_vfs` 对象的 xSleep() 方法实现此接口。若默认 VFS 的
xSleep() 方法实现不正确、或根本没有实现，则 `sqlite3_sleep()` 的行为可能偏离
前面几段的描述。

若向 `sqlite3_sleep()` 传负参数，结果因 VFS 和操作系统而异。有些系统把负参数
理解为永远睡眠的指令；另一些理解为完全不要睡眠。在 SQLite 版本 3.42.0 及以后，
传入 `sqlite3_sleep()` 的负参数在传递到 VFS 的 xSleep 方法之前会被改为零。

## 比较两个快照句柄的先后（Compare the ages of two snapshot handles）

```
int sqlite3_snapshot_cmp(
  sqlite3_snapshot *p1,
  sqlite3_snapshot *p2
);
```

`sqlite3_snapshot_cmp(P1, P2)` 接口用于比较两个有效快照句柄的先后。

若两个快照句柄不与同一个数据库文件关联，则比较结果未定义。

此外，仅当两个快照句柄都是在 wal 文件上次被删除之后、通过调用
`sqlite3_snapshot_get()` 获得的，比较结果才有效。当数据库改回回滚模式、或数据库
客户端数降到零时，wal 文件被删除。若任一快照句柄是在 wal 文件上次被删除之前获得
的，则此函数返回的值未定义。

否则，若 P1 引用的快照比 P2 旧，此 API 返回负值；若两个句柄引用同一个数据库
快照，返回零；若 P1 是比 P2 新的快照，返回正值。

此接口仅在用 `SQLITE_ENABLE_SNAPSHOT` 选项编译 SQLite 时可用。

## 销毁快照（Destroy a snapshot）

```
void sqlite3_snapshot_free(sqlite3_snapshot*);
```

`sqlite3_snapshot_free(P)` 接口销毁 sqlite3_snapshot P。为避免内存泄漏，应用必须
最终用此例程释放每个 sqlite3_snapshot 对象。

`sqlite3_snapshot_free()` 接口仅在用 `SQLITE_ENABLE_SNAPSHOT` 编译期选项时可用。

## 记录数据库快照（Record A Database Snapshot）

```
int sqlite3_snapshot_get(
  sqlite3 *db,
  const char *zSchema,
  sqlite3_snapshot **ppSnapshot
);
```

`sqlite3_snapshot_get(D,S,P)` 接口尝试创建一个新的 sqlite3_snapshot 对象，记录
数据库连接 D 中 schema S 的当前状态。成功时，`sqlite3_snapshot_get(D,S,P)` 接口
把指向新创建的 sqlite3_snapshot 对象的指针写入 *P 并返回 `SQLITE_OK`。若调用
此函数时 schema S 上还没有打开的读事务，则自动打开一个。

若读事务由本函数打开，则保证返回的快照对象在读事务关闭之前不会失效，即使数据库
写者或检查点操作试图使其失效。若调用此函数时读事务已经打开，则没有这种保证。此时，
对数据库的任何后续写或检查点操作都可能使返回的快照句柄失效，即使读事务保持打开。

要使此函数成功，以下条件必须全部成立。若调用 `sqlite3_snapshot_get()` 时以下任一
语句为假，则返回 `SQLITE_ERROR`，此时 *P 的最终值未定义。

- 数据库句柄不得处于自动提交模式。
- 数据库连接 D 的 schema S 必须是 WAL 模式数据库。
- 数据库连接 D 的 schema S 上不得有打开的写事务。
- 自当前 wal 文件在磁盘上创建以来（无论由哪个连接），必须已向其中写入一个或多个
  事务。这意味着：首次打开后、还没有 wal 文件的 WAL 模式数据库上不能立即获取快照，
  必须先向其中写入至少一个事务。

此函数也可能返回 `SQLITE_NOMEM`。若在数据库句柄处于自动提交模式时调用、但因其它
原因失败，则是否在 schema S 上打开读事务未定义。

成功调用 `sqlite3_snapshot_get()` 返回的 sqlite3_snapshot 对象必须用
`sqlite3_snapshot_free()` 释放，以避免内存泄漏。

`sqlite3_snapshot_get()` 接口仅在用 `SQLITE_ENABLE_SNAPSHOT` 编译期选项时可用。

## 在历史快照上开始读事务（Start a read transaction on an historical snapshot）

```
int sqlite3_snapshot_open(
  sqlite3 *db,
  const char *zSchema,
  sqlite3_snapshot *pSnapshot
);
```

`sqlite3_snapshot_open(D,S,P)` 接口要么开始一个新的读事务、要么升级数据库连接 D
上 schema S 的现有读事务，使该读事务指向历史快照 P，而不是数据库最近的变化。
`sqlite3_snapshot_open()` 接口成功时返回 `SQLITE_OK`，失败时返回适当的错误码。

要使调用成功，`sqlite3_snapshot_open(D,S,P)` 调用时数据库连接必须不处于自动提交
模式。
若 schema S 上已有打开的读事务，则数据库句柄不得有活动语句（已传给
`sqlite3_step()` 但未传给 `sqlite3_reset()` 或 `sqlite3_finalize()` 的 SELECT
语句）。若违反这两个条件中的任一个、或 schema S 不存在、或快照对象无效，则返回
`SQLITE_ERROR`。

若指定的快照已被检查点覆盖，对 `sqlite3_snapshot_open()` 的调用将无法打开。此时
返回 `SQLITE_ERROR_SNAPSHOT`。

若调用此函数时已有打开的读事务，则若返回 `SQLITE_ERROR`、`SQLITE_BUSY` 或
`SQLITE_ERROR_SNAPSHOT`，同一个读事务（在同一数据库快照上）保持打开。若返回其它
错误码——例如 `SQLITE_PROTOCOL` 或某个 `SQLITE_IOERR` 错误码——则读事务的最终
状态未定义。若返回 `SQLITE_OK`，则读事务现在打开在数据库快照 P 上。

若数据库连接 D 不知道 schema S 的数据库文件处于 WAL 模式，对
`sqlite3_snapshot_open(D,S,P)` 的调用将失败。若该数据库连接上先前没有 I/O 发生、
或数据库在该连接最近一次 I/O 之后进入 WAL 模式，则连接可能不知道数据库文件处于
WAL 模式。（提示：对新建的数据库连接运行 "PRAGMA application_id"，以使其准备好
使用快照。）

`sqlite3_snapshot_open()` 接口仅在用 `SQLITE_ENABLE_SNAPSHOT` 编译期选项时可用。

## 从 wal 文件恢复快照（Recover snapshots from a wal file）

```
int sqlite3_snapshot_recover(sqlite3 *db, const char *zDb);
```

若所有数据库连接都关闭后（要么通过使用 `SQLITE_FCNTL_PERSIST_WAL` 文件控制，
要么因为最后一个打开数据库的进程在未调用 `sqlite3_close()` 的情况下退出），WAL
文件仍留在磁盘上，随后在该数据库和 WAL 文件上打开新连接，则 `sqlite3_snapshot_open()`
接口只能打开添加到 WAL 文件的最后一个事务，尽管 WAL 文件还包含其它有效事务。

此函数尝试扫描数据库句柄 db 的数据库 zDb 关联的 WAL 文件，使所有有效快照可用于
`sqlite3_snapshot_open()`。若数据库上已有打开的读事务、或数据库不是 WAL 模式
数据库，则是错误。

成功时返回 `SQLITE_OK`，否则返回 SQLite 错误码。

此接口仅在用 `SQLITE_ENABLE_SNAPSHOT` 选项编译 SQLite 时可用。

## 已废弃的软堆上限接口（Deprecated Soft Heap Limit Interface）

```
void sqlite3_soft_heap_limit(int N);
```

这是 `sqlite3_soft_heap_limit64()` 接口的废弃版本。此例程仅为历史兼容而提供。
所有新应用都应使用 `sqlite3_soft_heap_limit64()` 接口而非本接口。

## 求值 SQL 语句（Evaluate An SQL Statement）

```
int sqlite3_step(sqlite3_stmt*);
```

在预编译语句用 `sqlite3_prepare_v2()`、`sqlite3_prepare_v3()`、
`sqlite3_prepare16_v2()`、`sqlite3_prepare16_v3()` 或传统接口
`sqlite3_prepare()`、`sqlite3_prepare16()` 之一准备之后，必须调用此函数一次或多次
来求值该语句。

`sqlite3_step()` 接口的行为细节取决于语句是用较新的 "vX" 接口
（`sqlite3_prepare_v3()`、`sqlite3_prepare_v2()`、`sqlite3_prepare16_v3()`、
`sqlite3_prepare16_v2()`）还是较旧的传统接口（`sqlite3_prepare()` 和
`sqlite3_prepare16()`）准备的。新应用推荐使用新的 "vX" 接口，但传统接口将继续
支持。

在传统接口中，返回值是 `SQLITE_BUSY`、`SQLITE_DONE`、`SQLITE_ROW`、`SQLITE_ERROR`
或 `SQLITE_MISUSE` 之一。用 "v2" 接口，也可能返回任何其它结果码或扩展结果码。

`SQLITE_BUSY` 表示数据库引擎无法取得完成工作所需的数据库锁。若语句是 COMMIT、
或发生在显式事务之外，则可以重试该语句。若语句不是 COMMIT、且发生在显式事务
之内，则应在继续前回滚事务。

`SQLITE_DONE` 表示语句已成功完成执行。除非先调用 `sqlite3_reset()` 把虚拟机
重置回初始状态，否则不应在此虚拟机上再次调用 `sqlite3_step()`。

若被执行的 SQL 语句返回任何数据，则每行新数据准备好供调用者处理时返回
`SQLITE_ROW`。可用列访问函数访问这些值。再次调用 `sqlite3_step()` 以取回下一行
数据。

`SQLITE_ERROR` 表示发生了运行时错误（如约束冲突）。不应再在此虚拟机上调用
`sqlite3_step()`。调用 `sqlite3_errmsg()` 可找到更多信息。用传统接口，调用
`sqlite3_reset()` 可获得更具体的错误码（例如 `SQLITE_INTERRUPT`、`SQLITE_SCHEMA`、
`SQLITE_CORRUPT` 等）。在 "v2" 接口中，更具体的错误码由 `sqlite3_step()` 直接
返回。

`SQLITE_MISUSE` 表示此例程被不适当地调用。也许它被调用在一个已终结的预编译语句
上、或一个先前已返回 `SQLITE_ERROR` 或 `SQLITE_DONE` 的语句上。也可能是同一时刻
有两个或多个线程使用同一个数据库连接。

对于直到并包括 3.6.23.1 的所有 SQLite 版本，在 `sqlite3_step()` 返回 `SQLITE_ROW`
以外的任何值之后、任何后续 `sqlite3_step()` 调用之前，都需要调用
`sqlite3_reset()`。用 `sqlite3_reset()` 重置预编译语句失败会导致
`sqlite3_step()` 返回 `SQLITE_MISUSE`。但在 3.6.23.1（2010-03-26）之后，
`sqlite3_step()` 开始在这种情形下自动调用 `sqlite3_reset()`，而不是返回
`SQLITE_MISUSE`。这不被视为兼容性破坏，因为任何收到 `SQLITE_MISUSE` 错误的应用
按定义就是坏的。`SQLITE_OMIT_AUTORESET` 编译期选项可用于恢复传统行为。

> 接口提示（Goofy Interface Alert）：在传统接口中，除 `SQLITE_BUSY` 和
> `SQLITE_MISUSE` 外的任何错误之后，`sqlite3_step()` API 总是返回通用错误码
> `SQLITE_ERROR`。必须调用 `sqlite3_reset()` 或 `sqlite3_finalize()` 才能找到更具体
> 地描述错误的错误码之一。我们承认这是笨拙的设计。此问题已在 "v2" 接口中修复。若用
> `sqlite3_prepare_v3()` 或 `sqlite3_prepare_v2()` 或 `sqlite3_prepare16_v2()` 或
> `sqlite3_prepare16_v3()` 而非传统 `sqlite3_prepare()` 和 `sqlite3_prepare16()`
> 接口准备所有 SQL 语句，则更具体的错误码由 `sqlite3_step()` 直接返回。
> 推荐使用 "vX" 接口。

## 判断预编译语句是否已被重置（Determine If A Prepared Statement Has Been Reset）

```
int sqlite3_stmt_busy(sqlite3_stmt*);
```

`sqlite3_stmt_busy(S)` 接口：若预编译语句 S 已调用过 `sqlite3_step(S)` 至少
一次、但既未运行到完成（`sqlite3_step(S)` 未返回 `SQLITE_DONE`）、也未用
`sqlite3_reset(S)` 重置，则返回真（非零）。若 S 是 NULL 指针，
`sqlite3_stmt_busy(S)` 接口返回假。若 S 非 NULL 指针、但又不是指向有效预编译语句
对象的指针，则行为未定义、且多半糟糕。

此接口可配合 `sqlite3_next_stmt()` 使用，找出与某个数据库连接关联的所有需要被
重置的预编译语句。例如可用于诊断例程，搜索持有事务打开的预编译语句。

## 改变预编译语句的 EXPLAIN 设置（Change The EXPLAIN Setting For A Prepared Statement）

```
int sqlite3_stmt_explain(sqlite3_stmt *pStmt, int eMode);
```

`sqlite3_stmt_explain(S,E)` 接口改变预编译语句 S 的 EXPLAIN 设置。若 E 为零，则
S 变成普通预编译语句。若 E 为 1，则 S 表现得仿佛其 SQL 文本以 "EXPLAIN" 开头。
若 E 为 2，则 S 表现得仿佛其 SQL 文本以 "EXPLAIN QUERY PLAN" 开头。

调用 `sqlite3_stmt_explain(S,E)` 可能使 S 被重新准备。SQLite 尽量避免重新准备，
但在第一次转入 EXPLAIN 或 EXPLAIN QUERY PLAN 模式时可能必须重新准备。

由于可能需要重新准备，若 S 因用 `sqlite3_prepare()` 而非较新的
`sqlite3_prepare_v2()` 或 `sqlite3_prepare_v3()` 接口创建、因而没有用于重新准备
的已保存 SQL 文本、无法重新准备，则对 `sqlite3_stmt_explain(S,E)` 的调用会以
`SQLITE_ERROR` 失败。

改变预编译语句的 EXPLAIN 设置不改变该语句的原始 SQL 文本。因此，若 SQL 文本
最初以 EXPLAIN 或 EXPLAIN QUERY PLAN 开头，但调用了 `sqlite3_stmt_explain(S,0)`
把语句转换为普通语句，则 EXPLAIN 或 EXPLAIN QUERY PLAN 关键字仍会出现在
`sqlite3_sql(S)` 输出中，尽管该语句现在表现得像普通 SQL 语句。

若 EXPLAIN 模式被成功改变，此例程返回 `SQLITE_OK`；若无法改变，返回错误码。
语句处于活动状态时不能改变 EXPLAIN 模式。因此，好的做法是在调用
`sqlite3_stmt_explain(S,E)` 之前立即调用 `sqlite3_reset(S)`。

## 查询预编译语句的 EXPLAIN 设置（Query The EXPLAIN Setting For A Prepared Statement）

```
int sqlite3_stmt_isexplain(sqlite3_stmt *pStmt);
```

`sqlite3_stmt_isexplain(S)` 接口：若预编译语句 S 是 EXPLAIN 语句返回 1，若语句 S
是 EXPLAIN QUERY PLAN 返回 2。若 S 是普通语句或 NULL 指针，
`sqlite3_stmt_isexplain(S)` 接口返回 0。

## 判断 SQL 语句是否写数据库（Determine If An SQL Statement Writes The Database）

```
int sqlite3_stmt_readonly(sqlite3_stmt *pStmt);
```

`sqlite3_stmt_readonly(X)` 接口：当且仅当预编译语句 X 不直接改变数据库文件的内容
时返回真（非零）。

注意：应用自定义的 SQL 函数或虚拟表可能作为副作用间接改变数据库。例如，若应用
定义一个调用 `sqlite3_exec()` 的函数 "eval()"，则以下 SQL 语句会通过副作用改变
数据库文件：

```
SELECT eval('DELETE FROM t1') FROM t2;
```

但因为 SELECT 语句不直接改变数据库文件，`sqlite3_stmt_readonly()` 仍会返回真。

BEGIN、COMMIT、ROLLBACK、SAVEPOINT 和 RELEASE 等事务控制语句会使
`sqlite3_stmt_readonly()` 返回真，因为这些语句本身并不真正修改数据库，而是控制
其它语句修改数据库的时机。ATTACH 和 DETACH 语句也使 `sqlite3_stmt_readonly()`
返回真，因为虽然这些语句改变数据库连接的配置，却不改变磁盘上数据库文件的内容。
`sqlite3_stmt_readonly()` 接口对 BEGIN 返回真，因为 BEGIN 只是设置内部标志；但
BEGIN IMMEDIATE 和 BEGIN EXCLUSIVE 命令确实会访问数据库文件（读取/写入或加锁），
因此对这些命令 `sqlite3_stmt_readonly()` 返回假。

若语句有任何可能改变数据库文件，此例程返回假。返回假并不保证语句会改变数据库
文件。例如，UPDATE 语句的 WHERE 子句可能使其成为空操作，但
`sqlite3_stmt_readonly()` 结果仍为假。类似地，CREATE TABLE IF NOT EXISTS 语句在
表已存在时是只读空操作，但对这样的语句 `sqlite3_stmt_readonly()` 仍返回假。

若预编译语句 X 是 EXPLAIN 或 EXPLAIN QUERY PLAN 语句，则 `sqlite3_stmt_readonly(X)`
返回与省略 EXPLAIN 或 EXPLAIN QUERY PLAN 前缀时相同的值。

## 清零扫描状态计数器（Zero Scan-Status Counters）

```
void sqlite3_stmt_scanstatus_reset(sqlite3_stmt*);
```

清零所有与 `sqlite3_stmt_scanstatus()` 相关的事件计数器。

此 API 仅在库用预处理器符号 `SQLITE_ENABLE_STMT_SCANSTATUS` 定义构建时可用。

## 预编译语句状态（Prepared Statement Status）

```
int sqlite3_stmt_status(sqlite3_stmt*, int op,int resetFlg);
```

每条预编译语句维护各种 `SQLITE_STMTSTATUS` 计数器，测量它执行特定操作的次数。
这些计数器可用于监控预编译语句的性能特征。例如，若表步进次数远远超过表搜索次数
或结果行数，则往往表明该预编译语句在做全表扫描而非使用索引。

此接口用于从预编译语句取回和重置计数器值。第一个参数是要查询的预编译语句对象。
第二个参数是要查询的特定 `SQLITE_STMTSTATUS` 计数器的整数代码。返回所请求计数器的
当前值。若 resetFlg 为真，则此接口调用返回后计数器被重置为零。

另见：sqlite3_status() 和 sqlite3_db_status()。

## 创建新的动态字符串对象（Create A New Dynamic String Object）

```
sqlite3_str *sqlite3_str_new(sqlite3*);
```

`sqlite3_str_new(D)` 接口分配并初始化一个新的 sqlite3_str 对象。为避免内存泄漏，
`sqlite3_str_new()` 返回的对象必须由随后的 `sqlite3_str_finish(X)` 调用释放。

`sqlite3_str_new(D)` 接口总是返回指向有效 sqlite3_str 对象的指针；不过若发生内存
不足错误，返回的对象可能是特殊的单例对象，它会静默拒绝新文本、总是从
`sqlite3_str_errcode()` 返回 `SQLITE_NOMEM`、总是为 `sqlite3_str_length()` 返回 0、
总是从 `sqlite3_str_finish(X)` 返回 NULL。把 `sqlite3_str_new(D)` 返回的值用作
任何其它 sqlite3_str 方法的 sqlite3_str 参数总是安全的。

`sqlite3_str_new(D)` 的 D 参数可以是 NULL。若 `sqlite3_str_new(D)` 的 D 参数非
NULL，则 sqlite3_str 对象中包含的字符串的最大长度将是 `sqlite3_limit(D,SQLITE_LIMIT_LENGTH)`
设置的值，而不是 `SQLITE_MAX_LENGTH`。

## 字符串 GLOB 匹配（String Globbing）

```
int sqlite3_strglob(const char *zGlob, const char *zStr);
```

`sqlite3_strglob(P,X)` 接口：当且仅当字符串 X 匹配 GLOB 模式 P 时返回零。
`sqlite3_strglob(P,X)` 使用的 GLOB 模式匹配定义与 SQLite 理解的 SQL 方言中
"X GLOB P" 算子相同。`sqlite3_strglob(P,X)` 函数区分大小写。

注意：此例程在匹配时返回零、字符串不匹配时返回非零，与 `sqlite3_stricmp()` 和
`sqlite3_strnicmp()` 相同。

另见：sqlite3_strlike()。

## 字符串 LIKE 匹配（String LIKE Matching）

```
int sqlite3_strlike(const char *zGlob, const char *zStr, unsigned int cEsc);
```

`sqlite3_strlike(P,X,E)` 接口：当且仅当字符串 X 匹配带转义字符 E 的 LIKE 模式 P
时返回零。`sqlite3_strlike(P,X,E)` 使用的 LIKE 模式匹配定义与 SQLite 理解的 SQL
方言中 "X LIKE P ESCAPE E" 算子相同。对于不带 ESCAPE 子句的 "X LIKE P"，把
`sqlite3_strlike(P,X,E)` 的 E 参数设为 0。与 LIKE 算子一样，`sqlite3_strlike(P,X,E)`
函数不区分大小写——等价的大写和小写 ASCII 字符互相匹配。

`sqlite3_strlike(P,X,E)` 函数匹配 Unicode 字符，但只有 ASCII 字符做大小写折叠。

注意：此例程在匹配时返回零、字符串不匹配时返回非零，与 `sqlite3_stricmp()` 和
`sqlite3_strnicmp()` 相同。

另见：sqlite3_strglob()。

## 底层系统错误码（Low-level system error code）

```
int sqlite3_system_errno(sqlite3*);
```

尝试返回导致最近一次 I/O 错误或打开文件失败的底层操作系统错误码或错误号。返回值
取决于操作系统。例如，在 unix 系统上，`sqlite3_open_v2()` 返回 `SQLITE_CANTOPEN`
之后，可调用此接口取回导致问题的底层 "errno"，如 ENOSPC、EAUTH、EISDIR 等。

## 提取表列的元数据（Extract Metadata About A Column Of A Table）

```
int sqlite3_table_column_metadata(
  sqlite3 *db,                /* 连接句柄 */
  const char *zDbName,        /* 数据库名或 NULL */
  const char *zTableName,     /* 表名 */
  const char *zColumnName,    /* 列名 */
  char const **pzDataType,    /* 输出：声明的数据类型 */
  char const **pzCollSeq,     /* 输出：排序规则名 */
  int *pNotNull,              /* 输出：存在 NOT NULL 约束则为真 */
  int *pPrimaryKey,           /* 输出：列为 PK 一部分则为真 */
  int *pAutoinc               /* 输出：列为自增则为真 */
);
```

`sqlite3_table_column_metadata(X,D,T,C,....)` 例程返回数据库连接 X 上、数据库 D
中、表 T 的列 C 的信息。若指定列存在，`sqlite3_table_column_metadata()` 接口返回
`SQLITE_OK`，并用适当值填充最后五个参数中的非 NULL 指针。若指定列不存在，
`sqlite3_table_column_metadata()` 接口返回 `SQLITE_ERROR`。若传给
`sqlite3_table_column_metadata()` 的列名参数是 NULL 指针，则此例程只检查表是否
存在：表存在返回 `SQLITE_OK`，不存在返回 `SQLITE_ERROR`。若调用
`sqlite3_table_column_metadata(X,D,T,C,...)` 时表名参数 T 是 NULL，则结果未定义。

列由此函数的第二、三、四个参数标识。第二个参数要么是包含指定表的数据库名
（即 "main"、"temp" 或附加数据库），要么是 NULL。若为 NULL，则用数据库引擎解析
非限定表引用所用的同一算法在所有附加数据库中搜索该表。

此函数的第三和第四个参数分别是所需列的表名和列名。

元数据通过写入作为此函数第 5 个及后续参数传入的内存位置返回。其中任何参数都可
为 NULL，此时省略元数据的对应元素。

为声明类型和排序规则返回的字符指针指向的内存，在下一次调用任何 SQLite API 函数
之前有效。

若指定表实际上是视图，则返回错误码。

若指定列是 "rowid"、"oid" 或 "_rowid_"，且表不是 WITHOUT ROWID 表、并已显式声明
INTEGER PRIMARY KEY 列，则输出参数为显式声明的列设置。若没有 INTEGER PRIMARY KEY
列，则 rowid 的输出设置如下：

```
data type: "INTEGER"
collation sequence: "BINARY"
not null: 0
primary key: 1
auto increment: 0
```

此函数导致所有数据库 schema 从磁盘读取并解析（若尚未完成此操作），并在加载
schema 时遇到任何错误则返回错误。

## 测试接口（Testing Interface）

```
int sqlite3_test_control(int op, ...);
```

`sqlite3_test_control()` 接口用于读取 SQLite 的内部状态，并出于测试目的向 SQLite
注入故障。第一个参数是一个操作码，决定所有后续参数的数量、含义和操作。

此接口不供应用使用。它只用于验证 SQLite 库的正确运行。取决于 SQLite 库的编译
方式，此接口可能不存在。

操作码的细节、它们的含义、它们接受的参数以及它们做什么，都可能随时更改而无需
通知。与大多数 SQLite API 不同，此函数不保证从一个版本到下一个版本一致运行。

## 测试库是否线程安全（Test To See If The Library Is Threadsafe）

```

int sqlite3_threadsafe(void);

```

`sqlite3_threadsafe()` 函数：当且仅当 SQLite 因 `SQLITE_THREADSAFE` 编译期选项
设为 0 而省略了互斥锁代码时返回零。

SQLite 可以带或不带互斥锁编译。当 `SQLITE_THREADSAFE` C 预处理宏为 1 或 2 时，
互斥锁被启用，SQLite 是线程安全的。当宏为 0 时，互斥锁被省略。没有互斥锁，从
多个线程并发使用 SQLite 是不安全的。

启用互斥锁会带来可衡量的性能损失。因此若速度至关重要，禁用互斥锁是合理的。但为
最大安全，应启用互斥锁。默认行为是启用互斥锁。

应用可用此接口确认它链接的 SQLite 版本是按 `SQLITE_THREADSAFE` 宏的理想设置编译
的。

此接口只报告 `SQLITE_THREADSAFE` 标志的编译期互斥锁设置。若 SQLite 用
`SQLITE_THREADSAFE=1` 或 =2 编译，则互斥锁默认启用，但可以用 `sqlite3_config()`
配合 `SQLITE_CONFIG_SINGLETHREAD`、`SQLITE_CONFIG_MULTITHREAD` 或
`SQLITE_CONFIG_SERIALIZED` 动词完全或部分禁用。`sqlite3_threadsafe()` 函数的返回
值只显示线程安全的编译期设置，不显示 `sqlite3_config()` 对该设置所做的任何运行时
更改。换句话说，`sqlite3_threadsafe()` 的返回值不受对 `sqlite3_config()` 调用的
影响。

更多信息见线程模式文档。

## SQL 跟踪钩子（SQL Trace Hook）

```
int sqlite3_trace_v2(
  sqlite3*,
  unsigned uMask,
  int(*xCallback)(unsigned,void*,void*,void*),
  void *pCtx
);
```

`sqlite3_trace_v2(D,M,X,P)` 接口对数据库连接 D 注册跟踪回调函数 X，使用属性掩码 M
和上下文指针 P。若 X 回调是 NULL 或 M 掩码为零，则禁用跟踪。M 参数应是零个或多个
`SQLITE_TRACE` 常量的按位 OR 组合。

对 `sqlite3_trace(D,X,P)` 或 `sqlite3_trace_v2(D,M,X,P)` 的每次调用都会覆盖
（取消）对数据库连接 D 先前所有 `sqlite3_trace(D,X,P)` 或 `sqlite3_trace_v2(D,M,X,P)`
调用。每个数据库连接最多有一个跟踪回调。

每当掩码 M 标识的任一事件发生时调用 X 回调。回调的整数返回值当前被忽略，但未来
版本可能改变。回调实现应返回零以保证未来兼容。

跟踪回调以四个参数调用：callback(T,C,P,X)。T 参数是 `SQLITE_TRACE` 常量之一，
指示回调被调用的原因。C 参数是上下文指针的副本。P 和 X 参数是指针，其含义取决于
T。

`sqlite3_trace_v2()` 接口旨在取代传统接口 `sqlite3_trace()` 和 `sqlite3_profile()`，
两者均已废弃。

## 确定数据库的事务状态（Determine the transaction state of a database）

```
int sqlite3_txn_state(sqlite3*,const char *zSchema);
```

`sqlite3_txn_state(D,S)` 接口返回数据库连接 D 中 schema S 的当前事务状态。若 S
是 NULL，则返回数据库连接 D 上任何 schema 的最高事务状态。事务状态（从低到高）为：

- `SQLITE_TXN_NONE`
- `SQLITE_TXN_READ`
- `SQLITE_TXN_WRITE`

若 `sqlite3_txn_state(D,S)` 的 S 参数不是有效 schema 的名字，则返回 -1。

## 解锁通知（Unlock Notification）

```
int sqlite3_unlock_notify(
  sqlite3 *pBlocked,                          /* 等待中的连接 */
  void (*xNotify)(void **apArg, int nArg),    /* 要调用的回调函数 */
  void *pNotifyArg                            /* 传给 xNotify 的参数 */
);
```

在共享缓存模式下运行，若无法取得共享缓存或其内单个表上所需的锁，数据库操作可能
以 `SQLITE_LOCKED` 错误失败。共享缓存锁定的描述见 SQLite Shared-Cache Mode。此
API 可用于注册一个回调：当当前持有所需锁的连接放弃该锁时，SQLite 会调用该回调。
此 API 仅在库用 `SQLITE_ENABLE_UNLOCK_NOTIFY` C 预处理器符号定义编译时可用。

另见：使用 SQLite 解锁通知特性。

共享缓存锁在数据库连接结束当前事务（无论是提交还是回滚）时释放。

当连接（称为被阻塞连接）无法取得共享缓存锁、向调用者返回 `SQLITE_LOCKED` 时，
已锁定所需资源的数据库连接（阻塞连接）的身份被内部存储。应用收到 `SQLITE_LOCKED`
错误后，可用被阻塞连接句柄作为第一个参数调用 `sqlite3_unlock_notify()` 方法注册
回调，该回调将在阻塞连接的当前事务结束时被调用。回调在结束阻塞连接事务的
`sqlite3_step` 或 `sqlite3_close` 调用内部被调用。

若在多线程应用中调用 `sqlite3_unlock_notify()`，则调用 `sqlite3_unlock_notify()`
时阻塞连接可能已经结束其事务。若发生这种情况，则从 `sqlite3_unlock_notify()`
调用内部立即调用指定回调。

若被阻塞连接试图取得共享缓存表的写锁，且当前有不止一个其它连接持有同一表的读锁，
则 SQLite 任意选择其中一个连接作为阻塞连接。

被阻塞连接最多可注册一个解锁通知回调。若在被阻塞连接已注册解锁通知回调时调用
`sqlite3_unlock_notify()`，则新回调取代旧回调。若以 NULL 指针作为第二个参数调用
`sqlite3_unlock_notify()`，则取消任何现有的解锁通知回调。被阻塞连接的解锁通知
回调也可通过用 `sqlite3_close()` 关闭被阻塞连接来取消。

解锁通知回调不可重入。若应用从解锁通知回调内部调用任何 sqlite3_xxx API 函数，
可能导致崩溃或死锁。

除非检测到死锁（见下），`sqlite3_unlock_notify()` 总是返回 `SQLITE_OK`。

**回调调用细节**

注册解锁通知回调时，应用提供单个 void\* 指针，回调被调用时传入该指针。但回调函数
的签名允许 SQLite 传入一个 void\* 上下文指针数组。传给解锁通知回调的第一个参数是
指向 void\* 指针数组的指针，第二个参数是数组中的条目数。

阻塞连接的事务结束时，可能有不止一个被阻塞连接注册了解锁通知回调。若两个或多个
这样的被阻塞连接指定了同一个回调函数，则不多次调用该回调函数，而是调用一次，并把
被阻塞连接指定的 void\* 上下文指针集合捆绑成数组传入。这给应用一个机会，对被解锁
的数据库连接集合相关的任何操作排定优先级。

**死锁检测**

假设注册解锁通知回调后，数据库在采取任何进一步动作之前等待回调发出（一个合理
假设），则使用此 API 可能导致应用死锁。例如，若连接 X 在等待连接 Y 的事务结束，
同时连接 Y 也在等待连接 X 的事务，则两个连接都不会继续，系统可能无限期死锁。

为避免这种情形，`sqlite3_unlock_notify()` 执行死锁检测。若某次对
`sqlite3_unlock_notify()` 的调用会使系统进入死锁状态，则返回 `SQLITE_LOCKED`、
不注册解锁通知回调。若连接 A 在连接 B 的事务结束时注册了解锁通知回调，且连接 B
自己在连接 A 的事务结束时注册了解锁通知回调，则称系统处于死锁状态。间接死锁也会
被检测到：若连接 B 在连接 C 的事务结束时注册了解锁通知回调，而连接 C 在等待连接 A，
则系统也被视为死锁。允许任意层数的间接。

**"DROP TABLE" 例外**

当对 `sqlite3_step()` 的调用返回 `SQLITE_LOCKED` 时，调用 `sqlite3_unlock_notify()`
几乎总是适当的。但有一个例外。执行 "DROP TABLE" 或 "DROP INDEX" 语句时，SQLite
检查是否有任何属于同一连接、当前正在执行的 SELECT 语句。若有，则返回
`SQLITE_LOCKED`。此时没有"阻塞连接"，因此调用 `sqlite3_unlock_notify()` 会导致
解锁通知回调被立即调用。若应用随后重试 "DROP TABLE" 或 "DROP INDEX" 查询，可能
导致无限循环。

解决此问题的一个方法是检查 `sqlite3_step()` 调用返回的扩展错误码。若有阻塞连接，
则扩展错误码被设为 `SQLITE_LOCKED_SHAREDCACHE`。否则，在特殊的 "DROP TABLE/INDEX"
情形下，扩展错误码就是 `SQLITE_LOCKED`。

## 数据变更通知回调（Data Change Notification Callbacks）

```
void *sqlite3_update_hook(
  sqlite3*,
  void(*)(void *,int ,char const *,char const *,sqlite3_int64),
  void*
);
```

`sqlite3_update_hook()` 接口对第一个参数标识的数据库连接注册回调函数，每当 rowid
表中的行被更新、插入或删除时调用它。此函数先前对同一数据库连接设置的任何回调都
被覆盖。

第二个参数是当 rowid 表中的行被更新、插入或删除时要调用的函数指针。用 NULL 指针
作为第二个参数调用 `sqlite3_update_hook()` 会禁用更新钩子。回调的第一个参数是
`sqlite3_update_hook()` 第三个参数的副本。回调的第二个参数是 `SQLITE_INSERT`、
`SQLITE_DELETE` 或 `SQLITE_UPDATE` 之一，取决于引起回调被调用的操作。回调的第三
和第四个参数包含指向受影响行所在数据库名和表名的指针。回调的最后一个参数是行的
rowid。在更新的情况下，这是更新发生后的 rowid。

修改内部系统表（即 sqlite_sequence）时不调用更新钩子。修改 WITHOUT ROWID 表时
不调用更新钩子。

在当前实现中，因 ON CONFLICT REPLACE 子句而删除冲突行时不调用更新钩子。用
truncate 优化删除行时也不调用更新钩子。本段定义的例外可能在 SQLite 未来版本中
改变。

更新钩子是在相应变更之前还是之后被调用，目前未指定，可能因变更类型而异。不要依赖
钩子调用相对触发钩子的操作最终结果的顺序。

更新钩子实现不得做任何会修改调用它的数据库连接的事情。任何修改数据库连接的动作
必须推迟到触发更新钩子的 `sqlite3_step()` 调用完成之后。注意：就本段中的"修改"
而言，`sqlite3_prepare_v2()` 和 `sqlite3_step()` 都会修改它们的数据库连接。

`sqlite3_update_hook(D,C,P)` 函数返回同一数据库连接 D 上先前调用的 P 参数；若是对
D 的第一次调用，则返回 NULL。

另见 sqlite3_commit_hook()、sqlite3_rollback_hook() 和 sqlite3_preupdate_hook()
接口。

## 函数的用户数据（User Data For Functions）

```
void *sqlite3_user_data(sqlite3_context*);
```

`sqlite3_user_data()` 接口返回指针的副本，即最初注册该应用自定义函数的
`sqlite3_create_function()` 和 `sqlite3_create_function16()` 例程的 pUserData
参数（第 5 个参数）。

此例程必须在应用自定义函数运行的同一线程中调用。

## 报告 sqlite3_value 对象的内部文本编码状态（Report the internal text encoding state of an sqlite3_value object）

```
int sqlite3_value_encoding(sqlite3_value*);
```

`sqlite3_value_encoding(X)` 接口根据值 X 的当前文本编码返回 `SQLITE_UTF8`、
`SQLITE_UTF16BE` 或 `SQLITE_UTF16LE` 之一，假设 X 的类型为 TEXT。若
`sqlite3_value_type(X)` 返回的不是 `SQLITE_TEXT`，则 `sqlite3_value_encoding(X)`
的返回值无意义。调用 `sqlite3_value_text(X)`、`sqlite3_value_text16(X)`、
`sqlite3_value_text16be(X)`、`sqlite3_value_text16le(X)`、`sqlite3_value_bytes(X)`
或 `sqlite3_value_bytes16(X)` 可能改变值 X 的编码，从而改变随后对
`sqlite3_value_encoding(X)` 调用的返回。

此例程供测试和验证 SQLite 实现的应用使用。此例程询问 sqlite3_value 对象的不透明
内部状态。普通应用不需要知道 sqlite3_value 对象的内部状态，因此不应使用此接口。

## 查找 SQL 值的子类型（Finding The Subtype Of SQL Values）

```
unsigned int sqlite3_value_subtype(sqlite3_value*);
```

`sqlite3_value_subtype(V)` 函数返回应用自定义 SQL 函数参数 V 的子类型。子类型
信息可用于从一个 SQL 函数向另一个 SQL 函数传递有限量的上下文。用
`sqlite3_result_subtype()` 例程设置 SQL 函数返回值的子类型。

每个调用此接口的应用自定义 SQL 函数，在注册函数时都应在文本编码参数中包含
`SQLITE_SUBTYPE` 属性。若省略 `SQLITE_SUBTYPE` 属性，则某些边角情形下
`sqlite3_value_subtype()` 可能返回零而非上游子类型。

## 确定虚拟表约束的排序规则（Determine The Collation For a Virtual Table Constraint）

```
const char *sqlite3_vtab_collation(sqlite3_index_info*,int);
```

此函数只能从虚拟表的 xBestIndex 方法内部调用。此函数返回一个字符串指针，该字符串
是对其参数标识的约束做文本比较时使用的适当排序规则的名字。

第一个参数必须是指向作为 xBestIndex() 方法第一参数的 `sqlite3_index_info` 对象的
指针。第二个参数必须是属于传给 xBestIndex 的 `sqlite3_index_info` 结构的
aConstraint[] 数组的索引。

> 重要：第一个参数必须与传入 xBestMethod() 方法的指针相同。第一个参数不能是指向
> 不同的 `sqlite3_index_info` 对象的指针，即使是完全相同的副本。

返回值计算如下：

- 若约束来自含 COLLATE 算子的 WHERE 子句表达式，则返回该 COLLATE 算子指定的排序
  规则名。
- 若没有 COLLATE 算子，但作为约束主体的列、通过传给 `sqlite3_declare_vtab()` 的
  CREATE TABLE 语句中列定义上的 COLLATE 子句指定了替代排序规则，则返回该替代
  排序规则名。
- 否则返回 "BINARY"。

## 虚拟表接口配置（Virtual Table Interface Configuration）

```
int sqlite3_vtab_config(sqlite3*, int op, ...);
```

此函数可由虚拟表实现的 xConnect 或 xCreate 方法调用，配置虚拟表接口的各个方面。

若在 xConnect 或 xCreate 虚拟表方法上下文之外调用此接口，则行为未定义。

在调用 `sqlite3_vtab_config(D,C,...)` 中，D 参数是正在创建虚拟表所在、并作为调用
`sqlite3_vtab_config()` 的 xConnect 或 xCreate 方法第一参数传入的数据库连接。
C 参数是虚拟表配置选项之一。C 之后参数的存在和含义取决于使用哪个虚拟表配置选项。

## 判断虚拟表查询是否为 DISTINCT（Determine if a virtual table query is DISTINCT）

```
int sqlite3_vtab_distinct(sqlite3_index_info*);
```

此 API 只能从虚拟表实现的 xBestIndex 方法内部使用。从 xBestIndex() 之外调用此
接口的结果未定义、且多半有害。

`sqlite3_vtab_distinct()` 接口返回 0 到 3 之间的整数。返回的整数给虚拟表关于查询
规划器希望输出如何排序的额外信息。只要虚拟表能满足查询规划器的排序要求，它就可以
设置 "orderByConsumed" 标志。

若 `sqlite3_vtab_distinct()` 接口返回 0，表示查询规划器需要虚拟表按
`sqlite3_index_info` 对象的 "nOrderBy" 和 "aOrderBy" 字段定义的排序顺序返回所有
行。这是默认预期。若虚拟表按排序顺序输出所有行，则无论 `sqlite3_vtab_distinct()`
返回什么，xBestIndex 方法设置 "orderByConsumed" 标志总是安全的。

若返回 1，表示只要 "aOrderBy" 字段标识的所有列上值相同的所有行相邻，查询规划器
就不需要行按排序顺序返回。查询规划器做 GROUP BY 时使用此模式。

若返回 2，表示只要 "aOrderBy" 标识的所有列上值相同的行相邻，查询规划器就不需要
行以任何特定顺序返回。此外，当两行或多行在 "colUsed" 标识的所有列上含相同值时，
结果中可可选地省略除一行外的所有此类行。虚拟表并非必须省略 "colUsed" 列上重复的
行，但若虚拟表能不过度费力地做到，可能有助于查询运行更快。此模式用于 DISTINCT
查询。

若返回 3，表示虚拟表必须按 "aOrderBy" 定义的顺序返回行，仿佛
`sqlite3_vtab_distinct()` 接口返回了 0。但若结果中两行或多行在 "colUsed" 标识的
所有列上含相同值，则可选地省略除一行外的所有此类行。与返回值 2 时一样，虚拟表
并非必须省略 "colUsed" 列上重复的行，但若能做到不过度费力，可能有助于查询运行
更快。此模式用于同时有 DISTINCT 和 ORDER BY 子句的查询。

下表总结了虚拟表允许基于 `sqlite3_vtab_distinct()` 返回值设置 "orderByConsumed"
标志的条件。此表是对前面四段的复述：

为排序目的比较虚拟表输出值是否相同时，两个 NULL 值被视为相同。换句话说，比较
算子为 "IS"（或 "IS NOT DISTINCT FROM"），而非 "=="。

若虚拟表实现无法满足上述要求，则不得在 `sqlite3_index_info` 对象中设置
"orderByConsumed" 标志，否则可能产生错误答案。

只要未设置 "orderByConsumed" 标志，虚拟表实现总是可以按任何想要的顺序返回行。
未设置该标志时，查询规划器会添加额外字节码，确保 SQL 查询返回的最终结果排序
正确。使用 "orderByConsumed" 标志和 `sqlite3_vtab_distinct()` 接口只是优化。
谨慎使用 `sqlite3_vtab_distinct()` 接口和 "orderByConsumed" 标志可能有助于针对
虚拟表的查询运行更快。另一方面，过度激进地在不该设置时设置 "orderByConsumed"
标志，可能导致 SQLite 返回错误结果。

## 在 xBestIndex 中识别并处理 IN 约束（Identify and handle IN constraints in xBestIndex）

```
int sqlite3_vtab_in(sqlite3_index_info*, int iCons, int bHandle);
```

此接口只能从虚拟表实现的 xBestIndex() 方法内部使用。从任何其它上下文调用此接口
的结果未定义、且多半有害。

形如 "column IN (...)" 的虚拟表约束作为 `SQLITE_INDEX_CONSTRAINT_EQ` 约束传给
xBestIndex 方法。若 xBestIndex 想使用此约束，它必须把对应的
aConstraintUsage[].argvIndex 设为正整数。然后，在通常的 IN 算子处理模式下，SQLite
生成字节码：对 IN 算子右侧的每个值调用一次 xFilter() 方法。因此虚拟表一次只能
看到 IN 算子右侧的单个值。

但在某些情况下，让虚拟表一次看到 IN 算子右侧的所有值会更好。`sqlite3_vtab_in()`
接口以两种方式促进这一点：

调用 `sqlite3_vtab_in(P,N,-1)`：当且仅当 P->aConstraint[N] 约束是能一次性处理的
IN 算子时返回真（非零）。换句话说，第三个参数为 -1 的 `sqlite3_vtab_in()` 是虚拟
表询问 SQLite：是否甚至可能对 IN 算子做一次性处理的机制。

第三个参数 F==1 或 F==0 的调用 `sqlite3_vtab_in(P,N,F)`，分别向 SQLite 表示虚拟
表想或不想一次性处理 IN 算子。因此当第三个参数 (F) 非负时，此接口是虚拟表告诉
SQLite 它想如何处理 IN 算子的机制。

`sqlite3_vtab_in(P,N,F)` 接口可在同一次 xBestIndex 方法调用中多次调用。对任何
给定的 P,N 对，`sqlite3_vtab_in(P,N,F)` 在同一次 xBestIndex 调用内返回值总是相同。
若接口返回真（非零），表示该约束是能一次性处理的 IN 算子。若约束不是 IN 算子、
或不能一次性处理，则接口返回假。

满足以下两个条件时选择 IN 算子的一次性处理：

- P->aConstraintUsage[N].argvIndex 值被设为正整数。这是虚拟表告诉 SQLite 它想
  使用第 N 个约束的方式。
- 对 F 非负的 `sqlite3_vtab_in(P,N,F)` 的最后一次调用有 F>=1。

若上述条件有一个或两个为假，则 SQLite 对该 IN 约束使用传统的一次处理一个值策略。
若两个条件都为真，则传给 xFilter 方法的第 argvIndex 个参数将是一个看似 NULL 的
sqlite3_value，但可传给 `sqlite3_vtab_in_first()` 和 `sqlite3_vtab_in_next()`
找出 IN 约束右侧的所有值。

## 判断虚拟表列访问是否用于 UPDATE（Determine If Virtual Table Column Access Is For UPDATE）

```
int sqlite3_vtab_nochange(sqlite3_context*);
```

若在虚拟表的 xColumn 方法内调用 `sqlite3_vtab_nochange(X)` 例程，则若列作为 UPDATE
操作的一部分被获取、且该列值不会改变，它可能返回真。虚拟表实现可把此提示视为允许：
用一个计算更便宜、且相应的 xUpdate 方法能理解为"无变更"值的返回值来替代。

若 xColumn 方法调用 `sqlite3_vtab_nochange()`、发现列未被 UPDATE 语句改变，则
xColumn 方法可以不设置结果、不调用任何 `sqlite3_result_xxxxx()` 接口就返回。
此时，xUpdate 方法中同一列的 `sqlite3_value_nochange(X)` 将返回真。

`sqlite3_vtab_nochange()` 例程是一个优化。即使 `sqlite3_vtab_nochange()` 接口总是
返回假，虚拟表实现也应继续给出正确答案。在当前实现中，对于增强的 UPDATE FROM
语句，`sqlite3_vtab_nochange()` 接口确实总是返回假。

## 确定虚拟表冲突策略（Determine The Virtual Table Conflict Policy）

```
int sqlite3_vtab_on_conflict(sqlite3 *);
```

此函数只能从虚拟表实现的 xUpdate 方法内部、为 INSERT 或 UPDATE 操作调用。根据触发
调用虚拟表 xUpdate 方法的 SQL 语句的 ON CONFLICT 模式，返回值为
`SQLITE_ROLLBACK`、`SQLITE_IGNORE`、`SQLITE_FAIL`、`SQLITE_ABORT` 或
`SQLITE_REPLACE` 之一。

## xBestIndex() 中的约束值（Constraint values in xBestIndex()）

```
int sqlite3_vtab_rhs_value(sqlite3_index_info*, int, sqlite3_value **ppVal);
```

此 API 只能从虚拟表实现的 xBestIndex 方法内部使用。从 xBestIndex 方法之外调用此
接口的结果未定义、且多半有害。

当从虚拟表实现的 xBestIndex 方法内部调用 `sqlite3_vtab_rhs_value(P,J,V)` 接口时，
P 是传入 xBestIndex 的 `sqlite3_index_info` 对象指针的副本，J 是 P->aConstraint[]
的基于 0 的索引，若该约束的右侧操作数已知，则此例程尝试把 *V 设为该操作数的值。
若右侧操作数未知，则把 *V 设为 NULL 指针。当且仅当 *V 被设为某个值时，
`sqlite3_vtab_rhs_value(P,J,V)` 接口返回 `SQLITE_OK`。若第 J 个约束的右侧不可用，
则 `sqlite3_vtab_rhs_value(P,J,V)` 接口返回 `SQLITE_NOTFOUND`。若发生问题，
`sqlite3_vtab_rhs_value()` 接口可返回 `SQLITE_OK` 或 `SQLITE_NOTFOUND` 之外的
结果码。

`sqlite3_vtab_rhs_value()` 接口通常只在约束的右侧操作数是原始 SQL 语句中的字面量
值时成功。若右侧操作数是表达式、对其它列的引用或宿主参数，则
`sqlite3_vtab_rhs_value()` 很可能返回 `SQLITE_NOTFOUND`。

某些约束（如 `SQLITE_INDEX_CONSTRAINT_ISNULL` 和 `SQLITE_INDEX_CONSTRAINT_ISNOTNULL`）
没有右侧操作数。对这类约束，`sqlite3_vtab_rhs_value()` 总是返回 `SQLITE_NOTFOUND`。

在 *V 中返回的 sqlite3_value 对象是受保护的 sqlite3_value，在 xBestIndex 方法调用
期间保持有效。xBestIndex 返回时，`sqlite3_vtab_rhs_value()` 返回的 sqlite3_value
对象被自动释放。

此例程名字里的 "_rhs_" 是 "Right-Hand Side"（右侧）的缩写。

## 配置自动检查点（Configure an auto-checkpoint）

```
int sqlite3_wal_autocheckpoint(sqlite3 *db, int N);
```

`sqlite3_wal_autocheckpoint(D,N)` 是 `sqlite3_wal_hook()` 的包装，它使数据库连接 D
上任何数据库在提交事务后，若预写日志文件中有 N 个或更多帧，则自动检查点。把 N
参数传零或负值会完全禁用自动检查点。

此函数注册的回调会替换任何用 `sqlite3_wal_hook()` 注册的现有回调。同样，用
`sqlite3_wal_hook()` 注册回调会禁用此函数配置的自动检查点机制。

wal_autocheckpoint pragma 可用于从 SQL 调用此接口。

此机制发起的检查点是 PASSIVE（被动）的。

每个新数据库连接默认启用自动检查点，阈值是 1000 页或
`SQLITE_DEFAULT_WAL_AUTOCHECKPOINT`。

仅当默认设置对特定应用被发现不够理想时，才有必要使用此接口。

## 检查点数据库（Checkpoint a database）

```
int sqlite3_wal_checkpoint(sqlite3 *db, const char *zDb);
```

`sqlite3_wal_checkpoint(D,X)` 等价于
`sqlite3_wal_checkpoint_v2(D,X,SQLITE_CHECKPOINT_PASSIVE,0,0)`。

简言之，`sqlite3_wal_checkpoint(D,X)` 使数据库连接 D 上数据库 X 的预写日志中的
内容被转移进数据库文件，并重置预写日志。更多信息见检查点文档。

此接口曾经是引起检查点发生的唯一方式。但后来加入了更新、更强大的
`sqlite3_wal_checkpoint_v2()` 接口。保留此接口是为了向后兼容，并方便需要手动开始
检查点、但不需要 `sqlite3_wal_checkpoint_v2()` 全部能力（及相应复杂性）的应用。

## 检查点数据库（Checkpoint a database）

```
int sqlite3_wal_checkpoint_v2(
  sqlite3 *db,                    /* 数据库句柄 */
  const char *zDb,                /* 附加数据库名（或 NULL） */
  int eMode,                      /* SQLITE_CHECKPOINT_* 值 */
  int *pnLog,                     /* 输出：WAL 日志的帧数 */
  int *pnCkpt                     /* 输出：已检查点的帧总数 */
);
```

`sqlite3_wal_checkpoint_v2(D,X,M,L,C)` 接口对数据库连接 D 的数据库 X 以模式 M 运行
检查点操作。状态信息写回 L 和 C 指向的整数。M 参数必须是有效的检查点模式：

`SQLITE_CHECKPOINT_PASSIVE`：不等待任何数据库读者或写者完成，尽可能多地检查点帧，
若日志中所有帧都被检查点则同步数据库文件。在 `SQLITE_CHECKPOINT_PASSIVE` 模式下
从不调用忙处理器回调。另一方面，若有并发的读者或写者，被动模式可能使检查点未完成。

`SQLITE_CHECKPOINT_FULL`：此模式阻塞（调用忙处理器回调），直到没有数据库写者、且
所有读者都从最新的数据库快照读取。然后检查点日志文件中的所有帧并同步数据库文件。
此模式在挂起时阻塞新的数据库写者，但允许新的数据库读者无阻碍地继续。

`SQLITE_CHECKPOINT_RESTART`：此模式与 `SQLITE_CHECKPOINT_FULL` 相同，额外之处是
检查点日志文件后阻塞（调用忙处理器回调），直到所有读者都只从数据库文件读取。这
确保下一个写者将从日志文件的开头重新开始。与 `SQLITE_CHECKPOINT_FULL` 一样，此
模式在挂起时阻塞新的数据库写者尝试，但不妨碍读者。

`SQLITE_CHECKPOINT_TRUNCATE`：此模式与 `SQLITE_CHECKPOINT_RESTART` 相同，额外之处
是在成功返回前把日志文件截断为零字节。

`SQLITE_CHECKPOINT_NOOP`：此模式总是检查点零帧。调用 NOOP 检查点的唯一理由是访问
`sqlite3_wal_checkpoint_v2()` 通过输出参数 *pnLog 和 *pnCkpt 返回的值。

若 pnLog 非 NULL，则把 *pnLog 设为日志文件中的帧总数；若因错误或数据库不在 WAL
模式而无法运行检查点，则设为 -1。若 pnCkpt 非 NULL，则把 *pnCkpt 设为日志文件中
已检查点的帧总数（包括函数被调用前已经检查点的帧）；若因错误或数据库不在 WAL 模式
而无法运行检查点，则设为 -1。注意：`SQLITE_CHECKPOINT_TRUNCATE` 成功完成后，日志
文件已被截断为零字节，因此 *pnLog 和 *pnCkpt 都会设为零。

所有调用都会获得数据库文件的排他 "checkpoint" 锁。若其它进程同时正在运行检查点
操作，则无法取得该锁，返回 `SQLITE_BUSY`。即使配置了忙处理器，在这种情况下也不会
调用它。

`SQLITE_CHECKPOINT_FULL`、RESTART 和 TRUNCATE 模式还获得数据库文件的排他 "writer"
锁。若无法立即取得写者锁、且配置了忙处理器，则调用它并重试写者锁，直到忙处理器
返回 0 或锁被成功取得。在等待数据库读者期间也调用忙处理器，如上所述。若忙处理器
在取得写者锁之前、或在等待数据库读者期间返回 0，则检查点操作从该点起以与
`SQLITE_CHECKPOINT_PASSIVE` 相同的方式继续——不进一步阻塞，尽可能多地检查点帧。
此时返回 `SQLITE_BUSY`。

若参数 zDb 是 NULL 或指向零长度字符串，则对数据库连接 db 附加的所有 WAL 数据库尝试
指定操作。此时写入输出参数 *pnLog 和 *pnCkpt 的值未定义。若处理一个或多个附加 WAL
数据库时遇到 `SQLITE_BUSY` 错误，仍会对任何剩余附加数据库尝试操作，最后返回
`SQLITE_BUSY`。若处理附加数据库时发生任何其它错误，则放弃处理、立即把错误码返回给
调用者。若处理附加数据库时没有遇到错误（`SQLITE_BUSY` 或其它），则返回 `SQLITE_OK`。

若数据库 zDb 是不在 WAL 模式的附加数据库的名字，则返回 `SQLITE_OK`，*pnLog 和
*pnCkpt 都设为 -1。若 zDb 非 NULL（或非零长度字符串）、且不是任何附加数据库的名字，
则向调用者返回 `SQLITE_ERROR`。

除非返回 `SQLITE_MISUSE`，`sqlite3_wal_checkpoint_v2()` 接口都会设置由
`sqlite3_errcode()` 和 `sqlite3_errmsg()` 查询的错误信息。

PRAGMA wal_checkpoint 命令可用于从 SQL 调用此接口。

## 预写日志提交钩子（Write-Ahead Log Commit Hook）

```
void *sqlite3_wal_hook(
  sqlite3*,
  int(*)(void *,sqlite3*,const char*,int),
  void*
);
```

`sqlite3_wal_hook()` 函数用于注册回调，每当数据提交到 wal 模式数据库时调用。

回调由 SQLite 在提交发生之后、数据库上关联的写锁释放之后调用，因此实现可按需读取、
写入或检查点数据库。

调用回调函数时传给它的第一个参数是注册回调时传给 `sqlite3_wal_hook()` 的第三个
参数的副本。第二个参数是数据库句柄的副本。第三个参数是被写入的数据库名——"main"
或 ATTACH 的数据库名。第四个参数是当前在预写日志文件中的页数，包括刚提交的页。

回调函数通常应返回 `SQLITE_OK`。若返回错误码，该错误会通过 SQLite 代码库传播回去，
使引发回调的语句报告错误，不过提交仍会发生。若回调返回 `SQLITE_ROW` 或
`SQLITE_DONE`，或返回不对应任何有效 SQLite 错误码的值，则结果未定义。

一个数据库句柄一次最多注册一个预写日志回调。调用 `sqlite3_wal_hook()` 会替换默认
行为或先前注册的预写日志回调。

返回值是先前调用（如果有）的第三个参数的副本，否则为 0。

`sqlite3_wal_autocheckpoint()` 接口和 wal_autocheckpoint pragma 都调用
`sqlite3_wal_hook()`，会覆盖任何先前的 `sqlite3_wal_hook()` 设置。

若用此函数设置了预写日志回调，则应定期调用 `sqlite3_wal_checkpoint_v2()` 或
PRAGMA wal_checkpoint，防止预写日志文件无界增长。

为回调传 NULL 指针会完全禁用自动检查点。要重新启用默认行为，调用
`sqlite3_wal_autocheckpoint(db,1000)` 或使用 PRAGMA wal_checkpoint。

## 结果码（Result Codes）

```
#define SQLITE_OK           0   /* 成功 */
/* beginning-of-error-codes */
#define SQLITE_ERROR        1   /* 一般错误 */
#define SQLITE_INTERNAL     2   /* SQLite 内部逻辑错误 */
#define SQLITE_PERM         3   /* 拒绝访问权限 */
#define SQLITE_ABORT        4   /* 回调例程请求中止 */
#define SQLITE_BUSY         5   /* 数据库文件被锁定 */
#define SQLITE_LOCKED       6   /* 数据库中的表被锁定 */
#define SQLITE_NOMEM        7   /* 一次 malloc() 失败 */
#define SQLITE_READONLY     8   /* 尝试写入只读数据库 */
#define SQLITE_INTERRUPT    9   /* 操作被 sqlite3_interrupt() 终止 */
#define SQLITE_IOERR       10   /* 发生某种磁盘 I/O 错误 */
#define SQLITE_CORRUPT     11   /* 数据库磁盘映像畸形 */
#define SQLITE_NOTFOUND    12   /* sqlite3_file_control() 中的未知操作码 */
#define SQLITE_FULL        13   /* 因数据库已满而插入失败 */
#define SQLITE_CANTOPEN    14   /* 无法打开数据库文件 */
#define SQLITE_PROTOCOL    15   /* 数据库锁协议错误 */
#define SQLITE_EMPTY       16   /* 仅供内部使用 */
#define SQLITE_SCHEMA      17   /* 数据库 schema 已改变 */
#define SQLITE_TOOBIG      18   /* 字符串或 BLOB 超过大小限制 */
#define SQLITE_CONSTRAINT  19   /* 因约束冲突而中止 */
#define SQLITE_MISMATCH    20   /* 数据类型不匹配 */
#define SQLITE_MISUSE      21   /* 库使用不正确 */
#define SQLITE_NOLFS       22   /* 使用宿主不支持的操作系统特性 */
#define SQLITE_AUTH        23   /* 授权被拒绝 */
#define SQLITE_FORMAT      24   /* 未使用 */
#define SQLITE_RANGE       25   /* sqlite3_bind 的第 2 个参数越界 */
#define SQLITE_NOTADB      26   /* 打开的不是数据库文件 */
#define SQLITE_NOTICE      27   /* 来自 sqlite3_log() 的通知 */
#define SQLITE_WARNING     28   /* 来自 sqlite3_log() 的警告 */
#define SQLITE_ROW         100  /* sqlite3_step() 还有一行就绪 */
#define SQLITE_DONE        101  /* sqlite3_step() 已完成执行 */

/* end-of-error-codes */

```

许多 SQLite 函数返回这里所示集合中的一个整数结果码，以指示成功或失败。

未来版本的 SQLite 可能加入新的错误码。

另见：扩展结果码定义

## 扩展结果码（Extended Result Codes）

```
#define SQLITE_ERROR_MISSING_COLLSEQ   (SQLITE_ERROR | (1

在其默认配置下，SQLite API 例程返回 30 个整数结果码之一。但经验表明，其中许多结果码
过于粗略，不能像程序员希望的那样提供关于问题的足够信息。为解决这个问题，较新的
SQLite 版本（3.3.8 2006-10-09 及以后）包含对提供更详细错误信息的额外结果码的支持。
这些扩展结果码用 `sqlite3_extended_result_codes()` API 按数据库连接逐个启用或禁用。
或者，最近一次错误的扩展码可用 `sqlite3_extended_errcode()` 获得。

## xAccess VFS 方法的标志（Flags for the xAccess VFS method）

```
#define SQLITE_ACCESS_EXISTS    0
#define SQLITE_ACCESS_READWRITE 1   /* 供 PRAGMA temp_store_directory 使用 */
#define SQLITE_ACCESS_READ      2   /* 未使用 */
```

这些整数常量可用作 sqlite3_vfs 对象的 xAccess 方法的第三个参数。它们决定 xAccess
方法在寻找什么类型的权限。用 `SQLITE_ACCESS_EXISTS`，xAccess 方法只检查文件是否
存在。用 `SQLITE_ACCESS_READWRITE`，xAccess 方法检查指定目录是否既可读又可写
（换句话说，目录内是否可添加、移除和重命名文件）。`SQLITE_ACCESS_READWRITE` 常量
目前只被 temp_store_directory pragma 使用，不过这在 SQLite 未来版本中可能改变。
用 `SQLITE_ACCESS_READ`，xAccess 方法检查文件是否可读。`SQLITE_ACCESS_READ` 常量
当前未使用，不过可能用于 SQLite 未来版本。

## 授权器动作码（Authorizer Action Codes）

```
/******************************************* 3rd ************ 4th ***********/
#define SQLITE_CREATE_INDEX          1   /* 索引名        表名             */
#define SQLITE_CREATE_TABLE          2   /* 表名          NULL             */
#define SQLITE_CREATE_TEMP_INDEX     3   /* 索引名        表名             */
#define SQLITE_CREATE_TEMP_TABLE     4   /* 表名          NULL             */
#define SQLITE_CREATE_TEMP_TRIGGER   5   /* 触发器名      表名             */
#define SQLITE_CREATE_TEMP_VIEW      6   /* 视图名        NULL             */
#define SQLITE_CREATE_TRIGGER        7   /* 触发器名      表名             */
#define SQLITE_CREATE_VIEW           8   /* 视图名        NULL             */
#define SQLITE_DELETE                9   /* 表名          NULL             */
#define SQLITE_DROP_INDEX           10   /* 索引名        表名             */
#define SQLITE_DROP_TABLE           11   /* 表名          NULL             */
#define SQLITE_DROP_TEMP_INDEX      12   /* 索引名        表名             */
#define SQLITE_DROP_TEMP_TABLE      13   /* 表名          NULL             */
#define SQLITE_DROP_TEMP_TRIGGER    14   /* 触发器名      表名             */
#define SQLITE_DROP_TEMP_VIEW       15   /* 视图名        NULL             */
#define SQLITE_DROP_TRIGGER         16   /* 触发器名      表名             */
#define SQLITE_DROP_VIEW            17   /* 视图名        NULL             */
#define SQLITE_INSERT               18   /* 表名          NULL             */
#define SQLITE_PRAGMA               19   /* Pragma 名     第 1 参数或 NULL  */
#define SQLITE_READ                 20   /* 表名          列名             */
#define SQLITE_SELECT               21   /* NULL          NULL             */
#define SQLITE_TRANSACTION          22   /* 操作          NULL             */
#define SQLITE_UPDATE               23   /* 表名          列名             */
#define SQLITE_ATTACH               24   /* 文件名        NULL             */
#define SQLITE_DETACH               25   /* 数据库名      NULL             */
#define SQLITE_ALTER_TABLE          26   /* 数据库名      表名             */
#define SQLITE_REINDEX              27   /* 索引名        NULL             */
#define SQLITE_ANALYZE              28   /* 表名          NULL             */
#define SQLITE_CREATE_VTABLE        29   /* 表名          模块名           */
#define SQLITE_DROP_VTABLE          30   /* 表名          模块名           */
#define SQLITE_FUNCTION             31   /* NULL          函数名           */
#define SQLITE_SAVEPOINT            32   /* 操作          保存点名称       */
#define SQLITE_COPY                  0   /* 不再使用 */
#define SQLITE_RECURSIVE            33   /* NULL          NULL             */
```

`sqlite3_set_authorizer()` 接口注册一个回调函数，用于授权某些 SQL 语句动作。回调的
第二个参数是一个整数代码，指定正在被授权什么动作。授权器回调可能被传的就是这些整数
动作码。

这些动作码值指示要授权哪种操作。授权回调函数的第 3 和第 4 个参数将是参数或 NULL，
取决于哪个码被用作第二个参数。授权器回调的第 5 个参数是数据库名（"main"、"temp"
等），如适用。授权器回调的第 6 个参数是对访问尝试负责的最内层触发器或视图的名字；
若访问尝试直接来自顶层 SQL 代码，则为 NULL。

## 文本编码（Text Encodings）

```
#define SQLITE_UTF8           1    /* IMP: R-37514-35566 */
#define SQLITE_UTF16LE        2    /* IMP: R-03371-37637 */
#define SQLITE_UTF16BE        3    /* IMP: R-51971-34154 */
#define SQLITE_UTF16          4    /* 使用本机字节序 */
#define SQLITE_ANY            5    /* 已废弃 */
#define SQLITE_UTF16_ALIGNED  8    /* 仅 sqlite3_create_collation 用 */
#define SQLITE_UTF8_ZT       16    /* 零结尾 UTF-8 */
```

这些常量定义表示 SQLite 支持的各种文本编码的整数代码。

`SQLITE_UTF8` 文本编码为 UTF-8。

`SQLITE_UTF16LE` 文本编码为 UTF-16，每个码点以"小端"表示——最低有效字节在前。这是
通常的编码，例如在 Windows 上。

`SQLITE_UTF16BE` 文本编码为 UTF-16，每个码点以"大端"表示——最高有效字节在前。这种
编码不太常见，但有时仍能见到，尤其是在较旧的系统上。

`SQLITE_UTF16` 文本编码为 UTF-16，每个码点根据宿主计算机的本机字节序以小端或大端
表示。

`SQLITE_ANY` 此编码值只能用于声明用 `sqlite3_create_function()` 等创建的应用自定义
SQL 函数的首选文本。若首选编码（`sqlite3_create_function()` 的第 4 个参数，即 eTextRep
参数）是 `SQLITE_ANY`，表示该函数对其参数文本编码没有偏好，可以接受 SQLite 核心觉得
方便的任意文本编码。此选项已废弃，新应用请勿使用。

`SQLITE_UTF16_ALIGNED` 此编码值可用作 `sqlite3_create_collation()` 等的第 3 个参数
（eTextRep 参数）。此编码值表示：创建的应用自定义排序规则期望其输入字符串为本机
字节序的 UTF-16，且字符串起始必须对齐到 2 字节边界。

`SQLITE_UTF8_ZT` 此选项只能用于指定传给 `sqlite3_result_text64()` 和
`sqlite3_bind_text64()` 的字符串的文本编码。`SQLITE_UTF8_ZT` 编码表示输入字符串
（称为 "z"）是 UTF-8 编码、且是零结尾的。若长度参数（称为 "n"）非负，此编码选项
表示调用者保证 z 数组至少包含 n+1 字节、且 z[n] 字节的值为零。此选项与 `SQLITE_UTF8`
给出相同输出，但在某些情况下可避免复制输入字符串的需要、从而更高效。但若 z 被分配
的字节少于 n+1、或 z[n] 字节不是零，则可能导致未定义行为。

## 基本数据类型（Fundamental Datatypes）

```
#define SQLITE_INTEGER  1
#define SQLITE_FLOAT    2
#define SQLITE_BLOB     4
#define SQLITE_NULL     5
#ifdef SQLITE_TEXT
# undef SQLITE_TEXT
#else
# define SQLITE_TEXT     3
#endif
#define SQLITE3_TEXT     3
```

SQLite 中的每个值都有五种基本数据类型之一：

- 64 位有符号整数
- 64 位 IEEE 浮点数
- 字符串
- BLOB
- NULL

这些常量是每种类型的代码。

注意：`SQLITE_TEXT` 常量在 SQLite 版本 2 中也被使用，但含义完全不同。同时链接 SQLite
版本 2 和版本 3 的软件应使用 `SQLITE3_TEXT`，而非 `SQLITE_TEXT`。

## CARRAY 表值函数的数据类型（Datatypes for the CARRAY table-valued function）

```
#define SQLITE_CARRAY_INT32     0    /* 数据是 32 位有符号整数 */
#define SQLITE_CARRAY_INT64     1    /* 数据是 64 位有符号整数 */
#define SQLITE_CARRAY_DOUBLE    2    /* 数据是双精度浮点数 */
#define SQLITE_CARRAY_TEXT      3    /* 数据是 char* */
#define SQLITE_CARRAY_BLOB      4    /* 数据是 struct iovec */
```

`sqlite3_carray_bind()` 接口的第五个参数必须是以下常量之一，指定被绑定到 carray
表值函数中的数组的数据类型。

## 检查点模式值（Checkpoint Mode Values）

```
#define SQLITE_CHECKPOINT_NOOP    -1  /* 完全不做任何工作 */
#define SQLITE_CHECKPOINT_PASSIVE  0  /* 尽量多做而不阻塞 */
#define SQLITE_CHECKPOINT_FULL     1  /* 等待写者，然后检查点 */
#define SQLITE_CHECKPOINT_RESTART  2  /* 同 FULL，但等待读者 */
#define SQLITE_CHECKPOINT_TRUNCATE 3  /* 同 RESTART，但也截断 WAL */
```

这些常量定义传给 `sqlite3_wal_checkpoint_v2()` 接口的第三个参数"检查点模式"的所有
有效值。每种检查点模式含义的细节见 `sqlite3_wal_checkpoint_v2()` 文档。

## 配置选项（Configuration Options）

```
#define SQLITE_CONFIG_SINGLETHREAD         1  /* nil */
#define SQLITE_CONFIG_MULTITHREAD          2  /* nil */
#define SQLITE_CONFIG_SERIALIZED           3  /* nil */
#define SQLITE_CONFIG_MALLOC               4  /* sqlite3_mem_methods* */
#define SQLITE_CONFIG_GETMALLOC            5  /* sqlite3_mem_methods* */
#define SQLITE_CONFIG_SCRATCH              6  /* 不再使用 */
#define SQLITE_CONFIG_PAGECACHE            7  /* void*, int sz, int N */
#define SQLITE_CONFIG_HEAP                 8  /* void*, int nByte, int min */
#define SQLITE_CONFIG_MEMSTATUS            9  /* boolean */
#define SQLITE_CONFIG_MUTEX               10  /* sqlite3_mutex_methods* */
#define SQLITE_CONFIG_GETMUTEX            11  /* sqlite3_mutex_methods* */
/* 之前的 SQLITE_CONFIG_CHUNKALLOC       12 现已不使用。 */
#define SQLITE_CONFIG_LOOKASIDE           13  /* int int */
#define SQLITE_CONFIG_PCACHE              14  /* no-op */
#define SQLITE_CONFIG_GETPCACHE           15  /* no-op */
#define SQLITE_CONFIG_LOG                 16  /* xFunc, void* */
#define SQLITE_CONFIG_URI                 17  /* int */
#define SQLITE_CONFIG_PCACHE2             18  /* sqlite3_pcache_methods2* */
#define SQLITE_CONFIG_GETPCACHE2          19  /* sqlite3_pcache_methods2* */
#define SQLITE_CONFIG_COVERING_INDEX_SCAN 20  /* int */
#define SQLITE_CONFIG_SQLLOG              21  /* xSqllog, void* */
#define SQLITE_CONFIG_MMAP_SIZE           22  /* sqlite3_int64, sqlite3_int64 */
#define SQLITE_CONFIG_WIN32_HEAPSIZE      23  /* int nByte */
#define SQLITE_CONFIG_PCACHE_HDRSZ        24  /* int *psz */
#define SQLITE_CONFIG_PMASZ               25  /* unsigned int szPma */
#define SQLITE_CONFIG_STMTJRNL_SPILL      26  /* int nByte */
#define SQLITE_CONFIG_SMALL_MALLOC        27  /* boolean */
#define SQLITE_CONFIG_SORTERREF_SIZE      28  /* int nByte */
#define SQLITE_CONFIG_MEMDB_MAXSIZE       29  /* sqlite3_int64 */
#define SQLITE_CONFIG_ROWID_IN_VIEW       30  /* int* */
```

这些常量是可作为 `sqlite3_config()` 接口第一个参数传入的可用整数配置选项。

`sqlite3_config()` 的大多数配置选项只在调用 `sqlite3_initialize()` 之前、或调用
`sqlite3_shutdown()` 之后起作用。此规则的少数例外称为"随时配置选项"。在
`sqlite3_initialize()` 和 `sqlite3_shutdown()` 调用之间、用非随时配置选项的首参数
调用 `sqlite3_config()`，是返回 `SQLITE_MISUSE` 的空操作。

随时配置选项的集合可能随 SQLite 版本而变（通过插入和/或删除）。截至 SQLite 版本
3.42.0，完整的随时配置选项集合是：

- `SQLITE_CONFIG_LOG`
- `SQLITE_CONFIG_PCACHE_HDRSZ`

未来版本的 SQLite 可能加入新的配置选项，现有配置选项可能停用。应用应检查
`sqlite3_config()` 的返回码，以确保调用生效。若调用了已停用或不支持的配置选项，
`sqlite3_config()` 接口将返回非零错误码。

**SQLITE_CONFIG_SINGLETHREAD**：此选项无参数。此选项把线程模式设置为 Single-thread
（单线程）。换句话说，它禁用所有互斥锁，使 SQLite 只能被单个线程使用。若 SQLite
用 `SQLITE_THREADSAFE=0` 编译期选项编译，则无法把线程模式从默认的 Single-thread
改变，因此用 `SQLITE_CONFIG_SINGLETHREAD` 配置选项调用 `sqlite3_config()` 会返回
`SQLITE_ERROR`。

**SQLITE_CONFIG_MULTITHREAD**：此选项无参数。此选项把线程模式设置为 Multi-thread
（多线程）。换句话说，它禁用数据库连接和预编译语句对象上的互斥锁。应用负责对
数据库连接和预编译语句的访问做串行化。但其它互斥锁被启用，因此只要没有两个线程
同时使用同一个数据库连接，SQLite 在多线程环境中使用就是安全的。若 SQLite 用
`SQLITE_THREADSAFE=0` 编译期选项编译，则无法设置 Multi-thread 线程模式，用
`SQLITE_CONFIG_MULTITHREAD` 配置选项调用 `sqlite3_config()` 会返回 `SQLITE_ERROR`。

**SQLITE_CONFIG_SERIALIZED**：此选项无参数。此选项把线程模式设置为 Serialized
（串行化）。换句话说，此选项启用所有互斥锁，包括数据库连接和预编译语句对象上的
递归互斥锁。在此模式（当 SQLite 用 `SQLITE_THREADSAFE=1` 编译时的默认模式）下，
SQLite 库自己串行化对数据库连接和预编译语句的访问，使应用可以在不同线程中同时
使用同一个数据库连接或同一个预编译语句。若 SQLite 用 `SQLITE_THREADSAFE=0` 编译期
选项编译，则无法设置 Serialized 线程模式，用 `SQLITE_CONFIG_SERIALIZED` 配置选项
调用 `sqlite3_config()` 会返回 `SQLITE_ERROR`。

**SQLITE_CONFIG_MALLOC**：此选项接受单个参数，即指向 `sqlite3_mem_methods` 结构
实例的指针。该参数指定替代低层内存分配例程，用于取代 SQLite 内建的内存分配例程。
SQLite 在 `sqlite3_config()` 调用返回前，会制作 `sqlite3_mem_methods` 结构内容的
私有副本。

**SQLITE_CONFIG_GETMALLOC**：此选项接受单个参数，即指向 `sqlite3_mem_methods`
结构实例的指针。`sqlite3_mem_methods` 结构被填上当前定义的内存分配例程。此选项
可用于用包装器重载默认内存分配例程，例如模拟内存分配失败或跟踪内存使用。

**SQLITE_CONFIG_SMALL_MALLOC**：此选项接受单个 int 类型参数，解释为布尔值；若为
真，则向 SQLite 提供提示：应尽量避免大的内存分配。若 SQLite 可自由做大内存分配，
它会运行得更快；但某些应用可能宁可运行慢些，以换取避免大分配才可能获得的内存碎片
保证。此提示通常关闭。

**SQLITE_CONFIG_MEMSTATUS**：此选项接受单个 int 类型参数，解释为布尔值，用于
启用或禁用内存分配统计的收集。当内存分配统计被禁用时，以下 SQLite 接口变为非
工作状态：

- sqlite3_hard_heap_limit64()
- sqlite3_memory_used()
- sqlite3_memory_highwater()
- sqlite3_soft_heap_limit64()
- sqlite3_status64()

内存分配统计默认启用，除非 SQLite 用 `SQLITE_DEFAULT_MEMSTATUS=0` 编译，此时内存
分配统计默认禁用。

**SQLITE_CONFIG_SCRATCH**：此选项不再使用。

**SQLITE_CONFIG_PAGECACHE**：此选项指定一个内存池，SQLite 可将其用于默认页缓存
实现下的数据库页缓存。若用 `SQLITE_CONFIG_PCACHE2` 加载了应用自定义的页缓存实现，
此配置选项是空操作。`SQLITE_CONFIG_PAGECACHE` 有三个参数：指向 8 字节对齐内存的
指针（pMem）、每个页缓存行的大小（sz）、以及缓存行数量（N）。sz 参数应是最大
数据库页的大小（512 到 65536 之间的 2 的幂）加上每个页头所需的额外字节。页头所需
的额外字节数可用 `SQLITE_CONFIG_PCACHE_HDRSZ` 确定。sz 参数大于必要值除了浪费内存
外没有坏处。pMem 参数必须是 NULL 指针，或指向至少 sz*N 字节的 8 字节对齐内存块的
指针，否则后续行为未定义。当 pMem 非 NULL 时，SQLite 将尽量用所提供的内存满足页
缓存需求，若页缓存行大于 sz 字节、或 pMem 缓冲区耗尽，则回退到 `sqlite3_malloc()`。
若 pMem 为 NULL 且 N 非零，则每个数据库连接从 `sqlite3_malloc()` 做一次初始批量
分配：若 N 为正，足够 N 个缓存行；若 N 为负，为 -1024*N 字节。若初始分配之外还需要
更多页缓存内存，则 SQLite 对每个额外缓存行分别向 `sqlite3_malloc()` 申请。

**SQLITE_CONFIG_HEAP**：此选项指定一个静态内存缓冲区，SQLite 将用它满足
`SQLITE_CONFIG_PAGECACHE` 提供之外的所有动态内存分配需求。`SQLITE_CONFIG_HEAP`
选项仅在 SQLite 用 `SQLITE_ENABLE_MEMSYS3` 或 `SQLITE_ENABLE_MEMSYS5` 编译时可用，
否则返回 `SQLITE_ERROR`。`SQLITE_CONFIG_HEAP` 有三个参数：指向内存的 8 字节对齐
指针、内存缓冲区中的字节数、最小分配大小。若第一个指针（内存指针）为 NULL，则
SQLite 恢复使用其默认内存分配器（系统 malloc() 实现），撤销任何先前对
`SQLITE_CONFIG_MALLOC` 的调用。若内存指针非 NULL，则启用替代内存分配器处理 SQLite
的所有内存分配需求。第一个指针（内存指针）必须对齐到 8 字节边界，否则 SQLite 后续
行为未定义。最小分配大小上限为 2**12；最小分配大小的合理值在 2**5 到 2**8 之间。

**SQLITE_CONFIG_MUTEX**：此选项接受单个参数，即指向 `sqlite3_mutex_methods` 结构
实例的指针。该参数指定替代低层互斥锁例程，用于取代 SQLite 内建的互斥锁例程。
SQLite 在 `sqlite3_config()` 调用返回前复制 `sqlite3_mutex_methods` 结构的内容。
若 SQLite 用 `SQLITE_THREADSAFE=0` 编译期选项编译，则整个互斥锁子系统从构建中省略，
因此用 `SQLITE_CONFIG_MUTEX` 配置选项调用 `sqlite3_config()` 会返回 `SQLITE_ERROR`。

**SQLITE_CONFIG_GETMUTEX**：此选项接受单个参数，即指向 `sqlite3_mutex_methods`
结构实例的指针。`sqlite3_mutex_methods` 结构被填上当前定义的互斥锁例程。此选项
可用于用包装器重载默认互斥锁分配例程，例如用于性能分析或测试的互斥锁使用跟踪。
若 SQLite 用 `SQLITE_THREADSAFE=0` 编译期选项编译，则整个互斥锁子系统从构建中
省略，因此用 `SQLITE_CONFIG_GETMUTEX` 配置选项调用 `sqlite3_config()` 会返回
`SQLITE_ERROR`。

**SQLITE_CONFIG_LOOKASIDE**：此选项接受两个参数，决定每个数据库连接上 lookaside
内存的默认大小。第一个参数是每个 lookaside 缓冲区槽的大小（"sz"），第二个参数是
分配给每个数据库连接的槽数（"cnt"）。`SQLITE_CONFIG_LOOKASIDE` 设置默认 lookaside
大小。`sqlite3_db_config()` 的 `SQLITE_DBCONFIG_LOOKASIDE` 选项可用于改变单个连接的
lookaside 配置。`-DSQLITE_DEFAULT_LOOKASIDE` 选项可在编译期改变默认 lookaside 配置。

**SQLITE_CONFIG_PCACHE2**：此选项接受单个参数，即指向 sqlite3_pcache_methods2 对象
的指针。此对象指定自定义页缓存实现的接口。SQLite 复制 sqlite3_pcache_methods2 对象。

**SQLITE_CONFIG_GETPCACHE2**：此选项接受单个参数，即指向 sqlite3_pcache_methods2
对象的指针。SQLite 把当前页缓存实现复制到该对象中。

**SQLITE_CONFIG_LOG**：此选项用于配置 SQLite 全局错误日志。（`SQLITE_CONFIG_LOG`
选项接受两个参数：指向调用签名为 void(*)(void*,int,const char*) 的函数的指针，以及
指向 void 的指针。若函数指针非 NULL，则由 `sqlite3_log()` 调用它处理每个日志事件。
若函数指针为 NULL，则 `sqlite3_log()` 接口变为空操作。传给 `SQLITE_CONFIG_LOG` 的
第二个参数 void 指针，在应用自定义日志函数每次被调用时作为其第一个参数传入。日志
函数的第二个参数是对应 `sqlite3_log()` 调用第一个参数的副本，用于表示结果码或扩展
结果码。传给日志函数的第三个参数是经 `sqlite3_snprintf()` 格式化后的日志消息。
SQLite 日志接口不可重入；应用提供的日志函数不得调用任何 SQLite 接口。在多线程应用
中，应用自定义日志函数必须线程安全。

**SQLITE_CONFIG_URI**：此选项接受单个 int 类型参数。若非零，则全局启用 URI 处理。
若参数为零，则全局禁用 URI 处理。若 URI 处理被全局启用，则传给 `sqlite3_open()`、
`sqlite3_open_v2()`、`sqlite3_open16()` 或作为 ATTACH 命令一部分指定的所有文件名都
被解释为 URI，无论打开数据库连接时是否设置 `SQLITE_OPEN_URI` 标志。若被全局禁用，
则只有打开数据库连接时设置了 `SQLITE_OPEN_URI` 标志，文件名才被解释为 URI。默认
情况下 URI 处理被全局禁用。可通过用 `SQLITE_USE_URI` 符号定义编译来改变默认值。

**SQLITE_CONFIG_COVERING_INDEX_SCAN**：此选项接受单个整数参数，解释为布尔值，用于
启用或禁用查询优化器中全表扫描使用覆盖索引。默认设置由 `SQLITE_ALLOW_COVERING_INDEX_SCAN`
编译期选项决定；若省略该编译期选项，则默认"开启"。能够禁用全表扫描使用覆盖索引，
是因为某些编码不正确的旧应用在该优化启用时可能出故障。提供禁用该优化的能力，可以让
旧的、有 bug 的应用代码即使配合更新版本的 SQLite 也能不加修改地工作。

**SQLITE_CONFIG_PCACHE 和 SQLITE_CONFIG_GETPCACHE**：这些选项已废弃，新代码不应
使用。它们为向后兼容而保留，但现在都是空操作。

**SQLITE_CONFIG_SQLLOG**：此选项仅在 sqlite 用 `SQLITE_ENABLE_SQLLOG` 预处理宏定义
编译时可用。第一个参数应是指向类型为 void(*)(void*,sqlite3*,const char*, int) 的
函数的指针。第二个参数类型应为 (void*)。库在三种不同情形下调用该回调，由第四个
参数的值标识。若第四个参数为 0，则作为第二个参数传入的数据库连接刚刚被打开；第三个
参数指向包含主数据库文件名的缓冲区。若第四个参数为 1，则第三个参数指向的 SQL 语句
刚刚被执行。若第四个参数为 2，则作为第二个参数传入的连接正在被关闭；此时第三个
参数传 NULL。在规范 SQLite 源代码树中的 "test_sqllog.c" 源文件里可以看到使用此配置
选项的示例。

**SQLITE_CONFIG_MMAP_SIZE**：`SQLITE_CONFIG_MMAP_SIZE` 接受两个 64 位整数
（sqlite3_int64）值，即默认 mmap 大小限制（PRAGMA mmap_size 的默认设置）和允许的
最大 mmap 大小限制。默认设置可由每个数据库连接用 PRAGMA mmap_size 命令或
`SQLITE_FCNTL_MMAP_SIZE` 文件控制覆盖。若必要，允许的最大 mmap 大小会被静默截断，
使其不超过 `SQLITE_MAX_MMAP_SIZE` 编译期选项设置的编译期最大 mmap 大小。若此选项
的任一参数为负，则该参数被改为其编译期默认值。

**SQLITE_CONFIG_WIN32_HEAPSIZE**：此选项仅在 SQLite 为 Windows 编译且定义了
`SQLITE_WIN32_MALLOC` 预处理宏时可用。`SQLITE_CONFIG_WIN32_HEAPSIZE` 接受一个
32 位无符号整数值，指定所创建堆的最大大小。

**SQLITE_CONFIG_PCACHE_HDRSZ**：此选项接受单个参数，即指向整数的指针，并把
`SQLITE_CONFIG_PAGECACHE` 中每页所需的额外字节数写入该整数。所需额外空间量可能随
编译器、目标平台和 SQLite 版本而变化。

**SQLITE_CONFIG_PMASZ**：此选项接受单个参数，即无符号整数，并把多线程排序器的
"Minimum PMA Size"（最小 PMA 大小）设置为该整数。默认的最小 PMA 大小由
`SQLITE_SORTER_PMASZ` 编译期选项设置。当启用多线程排序（用 PRAGMA threads 命令）、
且要排序的内容量超过页大小乘以 PRAGMA cache_size 设置与该值中较小者时，会启动新
线程协助排序操作。

**SQLITE_CONFIG_STMTJRNL_SPILL**：此选项接受单个参数，成为语句日志 spill 到磁盘的
阈值。语句日志保存在内存中，直到其大小（以字节计）超过此阈值，此时它们被写入磁盘。
若阈值是 -1，则语句日志总是完全保存在内存中。由于许多语句日志从不变得很大，把 spill
阈值设置为 64KiB 之类的值可大幅减少支持语句回滚所需的 I/O。此设置的默认值由
`SQLITE_STMTJRNL_SPILL` 编译期选项控制。

**SQLITE_CONFIG_SORTERREF_SIZE**：此选项接受单个 (int) 类型参数——排序引用大小阈值
的新值。通常，当 SQLite 使用外部排序按 ORDER BY 子句给记录排序时，调用者所需的全部
字段都存在于排序后的记录中。但若 SQLite 根据表列的声明类型判断其值可能非常大——大于
配置的排序引用大小阈值——则每条排序记录中存储一个引用，在按排序顺序返回记录时再从
数据库加载所需的列值。此选项的默认值是永不用此优化。为此选项指定负值会恢复默认行为。
此选项仅在 SQLite 用 `SQLITE_ENABLE_SORTER_REFERENCES` 编译期选项编译时可用。

**SQLITE_CONFIG_MEMDB_MAXSIZE**：此选项接受单个 sqlite3_int64 参数，即用
`sqlite3_deserialize()` 创建的内存数据库的默认最大大小。此默认最大大小可用
`SQLITE_FCNTL_SIZE_LIMIT` 文件控制对单个数据库上调或下调。若从不使用此配置设置，
则默认最大值由 `SQLITE_MEMDB_DEFAULT_MAXSIZE` 编译期选项决定。若未设置该编译期选项，
则默认最大值为 1073741824。

**SQLITE_CONFIG_ROWID_IN_VIEW**：此选项启用或禁用 VIEW 拥有 ROWID 的能力。只有
SQLite 用 `-DSQLITE_ALLOW_ROWID_IN_VIEW` 编译时才能启用该能力，此时该能力默认开启。
此配置选项查询当前设置、或把设置改为关闭或开启。参数是指向整数的指针。若该整数初始
值为 1，则激活 VIEW 拥有 ROWID 的能力；若初始值为零，则停用该能力；任何其它初始值
都使设置保持不变。更改（如有）后，若 VIEW 拥有 ROWID 的能力开启或关闭，则向该整数
写入 1 或 0。若 SQLite 未用 `-DSQLITE_ALLOW_ROWID_IN_VIEW` 编译（这是通常且推荐的
情况），则无论其初始值如何，该整数总是被填零。

## 数据库连接配置选项（Database Connection Configuration Options）

```
#define SQLITE_DBCONFIG_MAINDBNAME            1000 /* const char* */
#define SQLITE_DBCONFIG_LOOKASIDE             1001 /* void* int int */
#define SQLITE_DBCONFIG_ENABLE_FKEY           1002 /* int int* */
#define SQLITE_DBCONFIG_ENABLE_TRIGGER        1003 /* int int* */
#define SQLITE_DBCONFIG_ENABLE_FTS3_TOKENIZER 1004 /* int int* */
#define SQLITE_DBCONFIG_ENABLE_LOAD_EXTENSION 1005 /* int int* */
#define SQLITE_DBCONFIG_NO_CKPT_ON_CLOSE      1006 /* int int* */
#define SQLITE_DBCONFIG_ENABLE_QPSG           1007 /* int int* */
#define SQLITE_DBCONFIG_TRIGGER_EQP           1008 /* int int* */
#define SQLITE_DBCONFIG_RESET_DATABASE        1009 /* int int* */
#define SQLITE_DBCONFIG_DEFENSIVE             1010 /* int int* */
#define SQLITE_DBCONFIG_WRITABLE_SCHEMA       1011 /* int int* */
#define SQLITE_DBCONFIG_LEGACY_ALTER_TABLE    1012 /* int int* */
#define SQLITE_DBCONFIG_DQS_DML               1013 /* int int* */
#define SQLITE_DBCONFIG_DQS_DDL               1014 /* int int* */
#define SQLITE_DBCONFIG_ENABLE_VIEW           1015 /* int int* */
#define SQLITE_DBCONFIG_LEGACY_FILE_FORMAT    1016 /* int int* */
#define SQLITE_DBCONFIG_TRUSTED_SCHEMA        1017 /* int int* */
#define SQLITE_DBCONFIG_STMT_SCANSTATUS       1018 /* int int* */
#define SQLITE_DBCONFIG_REVERSE_SCANORDER     1019 /* int int* */
#define SQLITE_DBCONFIG_ENABLE_ATTACH_CREATE  1020 /* int int* */
#define SQLITE_DBCONFIG_ENABLE_ATTACH_WRITE   1021 /* int int* */
#define SQLITE_DBCONFIG_ENABLE_COMMENTS       1022 /* int int* */
#define SQLITE_DBCONFIG_FP_DIGITS             1023 /* int int* */
#define SQLITE_DBCONFIG_MAX                   1023 /* Largest DBCONFIG */
```

这些常量是作为 `sqlite3_db_config()` 接口第二参数传入的可用整数配置选项。

`sqlite3_db_config()` 接口是可变参数函数。它接受可变数量的参数，但总是至少两个。
传入 `sqlite3_db_config()` 的参数数量取决于哪个常量被用作第二个参数。本文档页把第二
参数之后的参数称为"arguments"。因此，当本页说"第 N 个 argument"时，意思是"配置选项
之后的第 N 个参数"或"`sqlite3_db_config()` 的第 (N+2) 个参数"。

未来版本的 SQLite 可能加入新的配置选项，现有配置选项可能停用。应用应检查
`sqlite3_db_config()` 的返回码，以确保调用生效。若调用了已停用或不支持的配置选项，
`sqlite3_db_config()` 接口将返回非零错误码。

**SQLITE_DBCONFIG_LOOKASIDE**：此选项用于调整数据库连接内 lookaside 内存分配器的
配置。`SQLITE_DBCONFIG_LOOKASIDE` 选项的参数不是通常的格式。它接受三个参数而非两个，
因此使用 `SQLITE_DBCONFIG_LOOKASIDE` 的 `sqlite3_db_config()` 调用总共应有五个参数。

第一个参数（"buf"）是指向用于 lookaside 内存的内存缓冲区的指针。第一个参数可以为
NULL，此时 SQLite 会用 `sqlite3_malloc()` 自己分配 lookaside 缓冲区。第二个参数
（"sz"）是每个 lookaside 缓冲区槽的大小。若 "sz" 小于 8，则禁用 lookaside。"sz"
参数应是小于 65536 的 8 的倍数。若 "sz" 不满足此约束，则其大小被减小直到满足。第三个
参数（"cnt"）是槽的数量。若 "cnt" 小于 1，则禁用 lookaside。要确保 "sz" 和 "cnt"
的乘积不超过 2,147,418,112。"cnt" 参数通常选择使 "sz" 和 "cnt" 的乘积小于 1,000,000。

若 "buf" 参数非 NULL，则它必须指向大小大于或等于 "sz" 和 "cnt" 乘积的内存缓冲区。
缓冲区必须对齐到 8 字节边界。数据库连接的 lookaside 内存配置只能在连接当前不使用
lookaside 内存时改变，换句话说，当 `SQLITE_DBSTATUS_LOOKASIDE_USED` 返回的值为零时。
在 lookaside 内存使用中试图改变其配置，会使配置保持不变并返回 `SQLITE_BUSY`。若
"buf" 参数为 NULL 且基于 "sz" 和 "cnt" 分配内存的尝试失败，则静默禁用 lookaside。

`SQLITE_CONFIG_LOOKASIDE` 配置选项可用于在初始化时设置默认 lookaside 配置。
`-DSQLITE_DEFAULT_LOOKASIDE` 选项可在编译期设置默认 lookaside 配置。lookaside 的
典型值是 "sz" 为 1200、"cnt" 为 40 到 100。

**SQLITE_DBCONFIG_ENABLE_FKEY**：此选项用于启用或禁用外键约束的强制。这与
PRAGMA foreign_keys 语句启用或禁用的设置相同。第一个参数是一个整数：0 禁用外键
强制、正数启用、负数保持外键强制不变。第二个参数是指向整数的指针，本次调用后向该
整数写入 0 或 1，指示外键强制是关闭还是开启。第二个参数可以为 NULL 指针，此时不
报告外键强制设置。

**SQLITE_DBCONFIG_ENABLE_TRIGGER**：此选项用于启用或禁用触发器。应有两个额外参数。
第一个参数是一个整数：0 禁用触发器、正数启用、负数保持设置不变。第二个参数是指向
整数的指针，本次调用后向该整数写入 0 或 1，指示触发器是禁用还是启用。第二个参数可以
为 NULL 指针，此时不报告触发器设置。

最初此选项禁用所有触发器。但从 SQLite 版本 3.35.0 起，即使此选项关闭，TEMP 触发器
仍被允许。换句话说，此选项现在只禁用主数据库 schema 或 ATTACH 数据库 schema 中的
触发器。

**SQLITE_DBCONFIG_ENABLE_VIEW**：此选项用于启用或禁用视图。必须有两个额外参数。
第一个参数是一个整数：0 禁用视图、正数启用、负数保持设置不变。第二个参数是指向整数
的指针，本次调用后向该整数写入 0 或 1，指示视图是禁用还是启用。第二个参数可以为
NULL 指针，此时不报告视图设置。

最初此选项禁用所有视图。但从 SQLite 版本 3.35.0 起，即使此选项关闭，TEMP 视图仍被
允许。换句话说，此选项现在只禁用主数据库 schema 或 ATTACH 数据库 schema 中的视图。

**SQLITE_DBCONFIG_ENABLE_FTS3_TOKENIZER**：此选项用于启用或禁用把 fts3_tokenizer()
函数——FTS3 全文搜索引擎扩展的一部分——用于非绑定参数作为参数。默认禁用这样做。
必须有两个额外参数。第一个参数是一个整数：若传 0，则禁用把 fts3_tokenizer() 用于
非绑定参数；若传正值，则启用；若传负值，则不修改此设置——可用于查询当前设置。第二个
参数是指向整数的指针，向该整数写入 0 或 1，指示此设置的当前值（若适用，在修改之后）。
第二个参数可以为 NULL 指针，此时不报告设置值。更多细节见 FTS3 文档。

**SQLITE_DBCONFIG_ENABLE_LOAD_EXTENSION**：此选项用于独立于 load_extension() SQL
函数启用或禁用 `sqlite3_load_extension()` 接口。`sqlite3_enable_load_extension()`
API 同时启用或禁用 C-API `sqlite3_load_extension()` 和 SQL 函数 load_extension()。
必须有两个额外参数。当此接口的第一个参数为 1 时，只启用 C-API，SQL 函数保持禁用。
若第一个参数为 0，则 C-API 和 SQL 函数都被禁用。若第一个参数为 -1，则 C-API 或 SQL
函数的状态都不做更改。第二个参数是指向整数的指针，本次调用后向该整数写入 0 或 1，
指示 `sqlite3_load_extension()` 接口是禁用还是启用。第二个参数可以为 NULL 指针，
此时不报告新设置。

**SQLITE_DBCONFIG_MAINDBNAME**：此选项用于改变 "main" 数据库 schema 的名字。此选项
不遵循通常的 `SQLITE_DBCONFIG` 参数格式。它恰好接受一个额外参数，使 `sqlite3_db_config()`
调用总共有三个参数。额外参数必须是指向常量 UTF8 字符串的指针，该字符串将成为取代
"main" 的新 schema 名。SQLite 不复制新主 schema 名字符串，因此应用必须确保传入
`SQLITE_DBCONFIG_MAINDBNAME` 的参数在数据库连接关闭之前保持不变。

**SQLITE_DBCONFIG_NO_CKPT_ON_CLOSE**：通常，当 WAL 模式数据库从数据库句柄关闭或
分离时，SQLite 检查是否有其它连接到同一数据库；若没有其它数据库连接（被关闭的连接是
该数据库最后一个打开的连接），则 SQLite 在关闭连接前执行检查点、并删除 WAL 文件。
`SQLITE_DBCONFIG_NO_CKPT_ON_CLOSE` 选项可用于覆盖该行为。传给此操作的第一个参数
（`sqlite3_db_config()` 的第三个参数）是一个整数：正数禁用关闭时检查点，零（默认）
启用，负数保持设置不变。第二个参数（第四个参数）是指向整数的指针，向该整数写入 0
或 1，指示关闭时检查点是否已被禁用——0 表示未被禁用，1 表示已禁用。

**SQLITE_DBCONFIG_ENABLE_QPSG**：此选项激活或停用查询规划器稳定性保证（QPSG）。
QPSG 激活时，单个 SQL 查询语句无论绑定参数的值如何，总是使用相同的算法。QPSG 禁用
某些查看绑定参数值的查询优化，这可能使某些查询变慢。但 QPSG 具有行为更可预测的优点。
QPSG 激活时，SQLite 在现场总是使用实验室测试时所用的相同查询计划。此设置的第一个
参数是一个整数：0 禁用 QPSG、正数启用、负数保持设置不变。第二个参数是指向整数的指针，
本次调用后向该整数写入 0 或 1，指示 QPSG 是禁用还是启用。

**SQLITE_DBCONFIG_TRIGGER_EQP**：默认情况下，EXPLAIN QUERY PLAN 命令的输出不包含
触发器程序执行的任何操作的输出。此选项用于设置或清除（默认）控制此行为的标志。传给
此操作的第一个参数是一个整数——正数启用触发器程序的输出、零禁用、负数保持设置不变。
第二个参数是指向整数的指针，向该整数写入 0 或 1，指示触发器输出是否已被禁用——0 表示
未被禁用，1 表示已禁用。

**SQLITE_DBCONFIG_RESET_DATABASE**：设置 `SQLITE_DBCONFIG_RESET_DATABASE` 标志，然后
运行 VACUUM，以把数据库重置回没有 schema、没有内容的空数据库。即使对严重损坏的数据库
文件，以下过程也有效：

- 若数据库连接是新打开的，通过准备然后丢弃某些针对数据库的查询、或调用
  `sqlite3_table_column_metadata()`（忽略任何错误）来确保它已读取数据库 schema。
  仅当应用希望重置后数据库保持 WAL 模式（若重置前处于 WAL 模式）时才需要此步骤。
- `sqlite3_db_config(db, SQLITE_DBCONFIG_RESET_DATABASE, 1, 0);`
- `sqlite3_exec(db, "VACUUM", 0, 0, 0);`
- `sqlite3_db_config(db, SQLITE_DBCONFIG_RESET_DATABASE, 0, 0);`

由于重置数据库是破坏性且不可逆的，该过程要求使用此晦涩的 API 和多个步骤，以帮助确保
它不会意外发生。由于此功能必须能重置损坏的数据库，而关闭虚拟表可能需要访问该损坏的
存储，库必须放弃任何已安装的虚拟表而不调用它们的 xDestroy() 方法。

**SQLITE_DBCONFIG_DEFENSIVE**：此选项激活或停用数据库连接的 "defensive"（防御）
标志。当防御标志启用时，允许普通 SQL 故意破坏数据库文件的语言特性被禁用。

被禁用的特性包括但不限于以下：

- PRAGMA writable_schema=ON 语句。
- PRAGMA journal_mode=OFF 语句。
- PRAGMA schema_version=N 语句。
- 对 sqlite_dbpage 虚拟表的写入。
- 对影子表的直接写入。

**SQLITE_DBCONFIG_WRITABLE_SCHEMA**：此选项激活或停用 "writable_schema" 标志。这与
设置 PRAGMA writable_schema=ON 或 PRAGMA writable_schema=OFF 有相同的效果、逻辑上
等价。此设置的第一个参数是一个整数：0 禁用 writable_schema、正数启用、负数保持设置
不变。第二个参数是指向整数的指针，本次调用后向该整数写入 0 或 1，指示 writable_schema
是启用还是禁用。

**SQLITE_DBCONFIG_LEGACY_ALTER_TABLE**：此选项激活或停用 ALTER TABLE RENAME 命令
的传统行为，使其表现得如同 3.24.0（2018-06-04）之前。更多信息见 ALTER TABLE RENAME
文档上的 "Compatibility Notice"。此特性也可用 PRAGMA legacy_alter_table 语句开关。

**SQLITE_DBCONFIG_DQS_DML**：此选项只对 DML 语句——即 DELETE、INSERT、SELECT 和
UPDATE 语句——激活或停用传统的双引号字符串字面量缺陷。此设置的默认值由
`-DSQLITE_DQS` 编译期选项决定。

**SQLITE_DBCONFIG_DQS_DDL**：此选项对 DDL 语句（如 CREATE TABLE 和 CREATE INDEX）
激活或停用传统的双引号字符串字面量缺陷。此设置的默认值由 `-DSQLITE_DQS` 编译期选项
决定。

**SQLITE_DBCONFIG_TRUSTED_SCHEMA**：此选项告诉 SQLite 假定数据库 schema 未被恶意
内容污染。当 `SQLITE_DBCONFIG_TRUSTED_SCHEMA` 选项被禁用时，SQLite 采取额外的防御
措施保护应用免受危害，包括：

- 禁止在触发器、视图、CHECK 约束、DEFAULT 子句、表达式索引、部分索引或生成列中
  使用 SQL 函数，除非这些函数被标记为 `SQLITE_INNOCUOUS`。
- 禁止在触发器或视图中使用虚拟表，除非这些虚拟表被标记为 `SQLITE_VTAB_INNOCUOUS`。

此设置默认为 "on"（开启）以保持传统兼容，但建议所有应用尽可能关闭它。此设置也可用
PRAGMA trusted_schema 语句控制。

**SQLITE_DBCONFIG_LEGACY_FILE_FORMAT**：此选项激活或停用传统文件格式标志。激活时，
此标志使所有新建的数据库文件的 schema 格式版本号（数据库头中偏移量 44 处的 4 字节
整数）为 1。这反过来意味着生成的数据库文件可被任何回溯到 3.0.0（2004-06-18）的
SQLite 版本读写。没有此设置，新建的数据库通常无法被 3.3.0（2006-01-11）之前的
SQLite 版本理解。写这些文字时，几乎不再需要生成完全回溯到 3.0.0 兼容的数据库文件，
因此此设置几乎没有实际用途，但提供它是为了让 SQLite 能继续声称能生成兼容 3.0.0
的新数据库文件。注意：当 `SQLITE_DBCONFIG_LEGACY_FILE_FORMAT` 设置开启时，VACUUM
命令在处理带生成列和降序索引的表时会以晦涩的错误失败。这不被视为 bug，因为 SQLite
3.3.0 及更早版本既不支持生成列、也不支持降序索引。

**SQLITE_DBCONFIG_STMT_SCANSTATUS**：此选项仅在 `SQLITE_ENABLE_STMT_SCANSTATUS`
构建中有用。此时，它设置或清除一个标志，启用 `sqlite3_stmt_scanstatus_v2()` 和
字节码虚拟表的 nexec、ncycle 列使用的运行时性能统计的收集。要收集统计，该标志必须
在 SQL 语句被准备和被 step 时都已在数据库句柄上设置。该标志默认设置（启用统计收集）。
此选项接受两个参数：一个整数和一个指向整数的指针。第一个参数是 1、0 或 -1，分别启用、
禁用或保持不变语句 scanstatus 选项。若第二个参数非 NULL，则把处理第一个参数后的语句
scanstatus 设置值写入第二个参数指向的整数。

**SQLITE_DBCONFIG_REVERSE_SCANORDER**：此选项改变表和索引被扫描的默认顺序，使扫描
从末尾开始向开头进行，而非从开头向末尾。设置 `SQLITE_DBCONFIG_REVERSE_SCANORDER`
与设置 PRAGMA reverse_unordered_selects 相同。此选项接受两个参数：一个整数和一个指向
整数的指针。第一个参数是 1、0 或 -1，分别启用、禁用或保持不变反向扫描顺序标志。若
第二个参数非 NULL，则根据处理第一个参数后反向扫描顺序标志是否被设置，向第二个参数
指向的整数写入 0 或 1。

**SQLITE_DBCONFIG_ENABLE_ATTACH_CREATE**：此选项启用或禁用 ATTACH DATABASE SQL 命令
在 ATTACH 命令命名的数据库文件尚不存在时创建新数据库文件的能力。ATTACH 创建新数据库
的能力默认启用。应用可用此 DBCONFIG 选项禁用或重新启用 ATTACH 创建新数据库文件的能力。
此选项接受两个参数：一个整数和一个指向整数的指针。第一个参数是 1、0 或 -1，分别启用、
禁用或保持不变 attach-create 标志。若第二个参数非 NULL，则根据处理第一个参数后
attach-create 标志是否被设置，向第二个参数指向的整数写入 0 或 1。

**SQLITE_DBCONFIG_ENABLE_ATTACH_WRITE**：此选项启用或禁用 ATTACH DATABASE SQL 命令
以写入方式打开数据库的能力。此能力默认启用。应用可用当前 DBCONFIG 选项禁用或重新启用
此能力。若此能力被禁用，ATTACH 命令仍会工作，但数据库将以只读方式打开。若此选项被
禁用，则用 ATTACH 创建新数据库的能力也被禁用，无论 `SQLITE_DBCONFIG_ENABLE_ATTACH_CREATE`
选项的值如何。此选项接受两个参数：一个整数和一个指向整数的指针。第一个参数是 1、0 或
-1，分别启用、禁用或保持不变以写入方式 ATTACH 另一数据库的能力。若第二个参数非 NULL，
则根据处理第一个参数后读写数据库 ATTACH 能力是启用还是禁用，向第二个参数指向的整数
写入 0 或 1。

**SQLITE_DBCONFIG_ENABLE_COMMENTS**：此选项启用或禁用 SQL 文本中包含注释的能力。
注释默认启用。应用可用此 DBCONFIG 选项禁用或重新启用 SQL 文本中的注释。此选项接受
两个参数：一个整数和一个指向整数的指针。第一个参数是 1、0 或 -1，分别启用、禁用或
保持不变 SQL 文本中使用注释的能力。若第二个参数非 NULL，则根据处理第一个参数后 SQL
文本中是否允许注释，向第二个参数指向的整数写入 0 或 1。

**SQLITE_DBCONFIG_FP_DIGITS**：`SQLITE_DBCONFIG_FP_DIGITS` 设置是一个小整数，决定
SQLite 在把浮点数（IEEE 754 "doubles"）转换为文本时尝试保留的有效数字位数。默认值
17（自 SQLite 版本 3.52.0 起）。此前的所有版本值为 15。此选项接受两个参数：一个整数
和一个指向整数的指针。第一个参数是 3 到 23 之间的小整数、或零。`FP_DIGITS` 设置被
改为该小整数；若第一个参数为零或越界，则保持不变。第二个参数是指向整数的指针。若该
指针非 NULL，则把（可能经第一个参数修改后的）`FP_DIGITS` 设置值写入第二个参数指向
的整数。

### SQLITE_DBCONFIG 选项的参数（Arguments To SQLITE_DBCONFIG Options）

大多数 `SQLITE_DBCONFIG` 选项接受两个参数，因此对 `sqlite3_db_config()` 的完整调用
总共有四个参数。第一个参数（`sqlite3_db_config()` 的第三个参数）是一个整数。第二个
参数是指向整数的指针。若第一个参数为 1，则选项被启用；若第一个整数参数为 0，则选项
被禁用；若第一个参数为 -1，则选项设置不变。第二个参数（指向整数的指针）可以为 NULL。
若第二个参数非 NULL，则根据应用第一个参数指定的任何更改后该设置是禁用还是启用，向
第二个参数指向的整数写入 0 或 1。

虽然大多数 `SQLITE_DBCONFIG` 选项使用上一段描述的参数格式，但 `SQLITE_DBCONFIG_MAINDBNAME`、
`SQLITE_DBCONFIG_LOOKASIDE` 和 `SQLITE_DBCONFIG_FP_DIGITS` 选项不同。细节见那些例外
选项的文档。

## 授权器返回码（Authorizer Return Codes）

```
#define SQLITE_DENY   1   /* 以错误中止 SQL 语句 */
#define SQLITE_IGNORE 2   /* 不允许访问，但不生成错误 */
```

授权器回调函数必须返回 `SQLITE_OK` 或这两个常量之一，以向 SQLite 指示该动作是否被
允许。更多信息见授权器文档。

注意：`SQLITE_IGNORE` 也被用作 `sqlite3_vtab_on_conflict()` 接口返回的冲突解决模式
之一。

## sqlite3_deserialize() 的标志（Flags for sqlite3_deserialize()）

```
#define SQLITE_DESERIALIZE_FREEONCLOSE 1 /* 关闭时调用 sqlite3_free() */
#define SQLITE_DESERIALIZE_RESIZEABLE  2 /* 用 sqlite3_realloc64() 调整大小 */
#define SQLITE_DESERIALIZE_READONLY    4 /* 数据库只读 */
```

以下是 `sqlite3_deserialize(D,S,P,N,M,F)` 接口第 6 个参数（F 参数）的允许值。

`SQLITE_DESERIALIZE_FREEONCLOSE` 表示 P 参数中的数据库序列化存放在从
`sqlite3_malloc64()` 获得的内存中，SQLite 应接管这块内存的所有权，并在用完时自动
释放它。没有此标志，调用者负责释放任何动态分配的内存。

`SQLITE_DESERIALIZE_RESIZEABLE` 标志表示允许 SQLite 用对 `sqlite3_realloc64()` 的
调用增大数据库的大小。此标志只应在同时使用 `SQLITE_DESERIALIZE_FREEONCLOSE` 时使用。
没有此标志，反序列化后的数据库不能增长到超过 M 参数指定的字节数。

`SQLITE_DESERIALIZE_READONLY` 标志表示反序列化后的数据库应被视为只读。

## 函数标志（Function Flags）

```
#define SQLITE_DETERMINISTIC    0x000000800
#define SQLITE_DIRECTONLY       0x000080000
#define SQLITE_SUBTYPE          0x000100000
#define SQLITE_INNOCUOUS        0x000200000
#define SQLITE_RESULT_SUBTYPE   0x001000000
#define SQLITE_SELFORDER1       0x002000000
```

这些常量可随首选文本编码 OR 起来，作为 `sqlite3_create_function()`、
`sqlite3_create_function16()` 或 `sqlite3_create_function_v2()` 的第四个参数。

**SQLITE_DETERMINISTIC**：此标志表示新函数在输入参数相同时总是给出相同的输出。例如
abs() 函数是确定性的，但 randomblob() 不是。函数必须确定性才能用于某些上下文，例如
部分索引的 WHERE 子句或生成列中。SQLite 也可能通过把确定性函数从内循环中提取出来来
优化它们。

**SQLITE_DIRECTONLY**：此标志表示该函数只能从顶层 SQL 调用，不能在视图或触发器中
使用，也不能用于 CHECK 约束、DEFAULT 子句、表达式索引、部分索引或生成列等 schema
结构中。

推荐为任何有副作用、或可能泄露敏感信息的应用自定义 SQL 函数设置 `SQLITE_DIRECTONLY`
标志。这将防止以下攻击：应用被骗使用一个 schema 被秘密修改、以有害方式调用应用自定义
函数的数据库文件。

有些人说，无论是否涉及安全敏感，对所有应用自定义 SQL 函数设置 `SQLITE_DIRECTONLY`
都是良好实践，因为这样做能防止这些函数被用在数据库 schema 内，从而确保数据库可用不
访问应用自定义函数的通用工具（如 CLI）检查和修改。

**SQLITE_INNOCUOUS**：此标志表示该函数即使被误用也不大可能引起问题。无害函数应无
副作用、且除了输入参数外不依赖任何值。abs() 函数是无害函数的一个例子。load_extension()
SQL 函数因其副作用而非无害。`SQLITE_INNOCUOUS` 与 `SQLITE_DETERMINISTIC` 类似，但
不完全相同。random() 函数是无害但不确定的函数的一个例子。某些加强的安全设置
（`SQLITE_DBCONFIG_TRUSTED_SCHEMA` 和 PRAGMA trusted_schema=OFF）

禁用视图和触发器内、以及
CHECK 约束、DEFAULT 子句、表达式索引、部分索引和生成列等 schema 结构中的 SQL 函数，
除非函数被标记为 `SQLITE_INNOCUOUS`。大多数内建函数都是无害的。建议开发者对应用
自定义函数避免使用 `SQLITE_INNOCUOUS` 标志，除非该函数经过仔细审计、确认没有潜在
危害安全性的副作用和信息泄漏。

**SQLITE_SUBTYPE**：此标志向 SQLite 表示：函数可能调用 `sqlite3_value_subtype()`
来检查其参数的子类型。此标志指示 SQLite 省略某些可能干扰 `sqlite3_value_subtype()`
函数的边角优化，这些优化会使其返回零而非正确的子类型。所有调用
`sqlite3_value_subtype()` 的 SQL 函数都应有此属性。若省略 `SQLITE_SUBTYPE` 属性，
则 `sqlite3_value_subtype()` 的返回值有时可能为零，即使函数参数表达式指定了非零
子类型。

**SQLITE_RESULT_SUBTYPE**：此标志向 SQLite 表示：函数可能调用
`sqlite3_result_subtype()` 使其结果关联一个子类型。每个调用 `sqlite3_result_subtype()`
的函数都应有此属性。若没有，则当函数用作表达式索引中的项时，对
`sqlite3_result_subtype()` 的调用可能变成空操作。另一方面，从不调用
`sqlite3_result_subtype()` 的 SQL 函数应避免设置此属性，因为此属性的目的是禁用某些
与子类型不兼容的优化。

**SQLITE_SELFORDER1**：此标志表示该函数是聚合函数，内部对提供给第一个参数的值排序。
带单个 ORDER BY 项的有序集合聚合 SQL 记法可用于调用此函数。若对缺少此标志的函数使用
有序集合聚合记法，则报错。注意：有序集合聚合语法仅在 SQLite 用
`-DSQLITE_ENABLE_ORDERED_SET_AGGREGATES` 编译期选项构建时可用。

## 冲突解决模式（Conflict resolution modes）

```
#define SQLITE_ROLLBACK 1
/* #define SQLITE_IGNORE 2 // 也供 sqlite3_authorizer() 回调使用 */
#define SQLITE_FAIL     3
/* #define SQLITE_ABORT 4  // 也是错误码 */
#define SQLITE_REPLACE  5
```

这些常量由 `sqlite3_vtab_on_conflict()` 返回，告知虚拟表实现正在求值的 SQL 语句的
ON CONFLICT 模式。

注意：`SQLITE_IGNORE` 常量也被用作 `sqlite3_set_authorizer()` 回调的潜在返回值，
`SQLITE_ABORT` 也是结果码。

## 标准文件控制操作码（Standard File Control Opcodes）

```
#define SQLITE_FCNTL_LOCKSTATE               1
#define SQLITE_FCNTL_GET_LOCKPROXYFILE       2
#define SQLITE_FCNTL_SET_LOCKPROXYFILE       3
#define SQLITE_FCNTL_LAST_ERRNO              4
#define SQLITE_FCNTL_SIZE_HINT               5
#define SQLITE_FCNTL_CHUNK_SIZE              6
#define SQLITE_FCNTL_FILE_POINTER            7
#define SQLITE_FCNTL_SYNC_OMITTED            8
#define SQLITE_FCNTL_WIN32_AV_RETRY          9
#define SQLITE_FCNTL_PERSIST_WAL            10
#define SQLITE_FCNTL_OVERWRITE              11
#define SQLITE_FCNTL_VFSNAME                12
#define SQLITE_FCNTL_POWERSAFE_OVERWRITE    13
#define SQLITE_FCNTL_PRAGMA                 14
#define SQLITE_FCNTL_BUSYHANDLER            15
#define SQLITE_FCNTL_TEMPFILENAME           16
#define SQLITE_FCNTL_MMAP_SIZE              18
#define SQLITE_FCNTL_TRACE                  19
#define SQLITE_FCNTL_HAS_MOVED              20
#define SQLITE_FCNTL_SYNC                   21
#define SQLITE_FCNTL_COMMIT_PHASETWO        22
#define SQLITE_FCNTL_WIN32_SET_HANDLE       23
#define SQLITE_FCNTL_WAL_BLOCK              24
#define SQLITE_FCNTL_ZIPVFS                 25
#define SQLITE_FCNTL_RBU                    26
#define SQLITE_FCNTL_VFS_POINTER            27
#define SQLITE_FCNTL_JOURNAL_POINTER        28
#define SQLITE_FCNTL_WIN32_GET_HANDLE       29
#define SQLITE_FCNTL_PDB                    30
#define SQLITE_FCNTL_BEGIN_ATOMIC_WRITE     31
#define SQLITE_FCNTL_COMMIT_ATOMIC_WRITE    32
#define SQLITE_FCNTL_ROLLBACK_ATOMIC_WRITE  33
#define SQLITE_FCNTL_LOCK_TIMEOUT           34
#define SQLITE_FCNTL_DATA_VERSION           35
#define SQLITE_FCNTL_SIZE_LIMIT             36
#define SQLITE_FCNTL_CKPT_DONE              37
#define SQLITE_FCNTL_RESERVE_BYTES          38
#define SQLITE_FCNTL_CKPT_START             39
#define SQLITE_FCNTL_EXTERNAL_READER        40
#define SQLITE_FCNTL_CKSM_FILE              41
#define SQLITE_FCNTL_RESET_CACHE            42
#define SQLITE_FCNTL_NULL_IO                43
#define SQLITE_FCNTL_BLOCK_ON_CONNECT       44
#define SQLITE_FCNTL_FILESTAT               45
```

这些整数常量是 sqlite3_io_methods 对象的 xFileControl 方法和
`sqlite3_file_control()` 接口的操作码。

`SQLITE_FCNTL_LOCKSTATE` 操作码用于调试。此操作码使 xFileControl 方法把锁的当前状态
（`SQLITE_LOCK_NONE`、`SQLITE_LOCK_SHARED`、`SQLITE_LOCK_RESERVED`、
`SQLITE_LOCK_PENDING` 或 `SQLITE_LOCK_EXCLUSIVE` 之一）写入 pArg 参数指向的整数。
此能力仅在 SQLite 用 `SQLITE_DEBUG` 编译时可用。

`SQLITE_FCNTL_SIZE_HINT` 操作码由 SQLite 用来给 VFS 层提示：在当前事务期间数据库文件
将增长到多大。此提示不保证准确，但通常接近。底层 VFS 可能选择基于此提示预分配数据库
文件空间，以帮助对数据库文件的写入运行更快。

`SQLITE_FCNTL_SIZE_LIMIT` 操作码由实现 `sqlite3_deserialize()` 的内存 VFS 用来设置
内存数据库大小的上限。参数是指向 sqlite3_int64 的指针。若指向的整数为负，则用当前
限制填充它。否则把限制设置为所指向整数的值与当前数据库大小中较大者。所指向的整数被
设置为新限制。

`SQLITE_FCNTL_CHUNK_SIZE` 操作码用于请求 VFS 按用户指定大小的块来扩展和截断数据库
文件。`sqlite3_file_control()` 的第四个参数应指向包含指定数据库要用的新块大小的整数
（类型 int）。以大块（例如每次 1MB）分配数据库文件空间可能减少文件系统碎片、提高某些
系统上的性能。

`SQLITE_FCNTL_FILE_POINTER` 操作码用于获得与特定数据库连接关联的 sqlite3_file 对象的
指针。另见 `SQLITE_FCNTL_JOURNAL_POINTER`。

`SQLITE_FCNTL_JOURNAL_POINTER` 操作码用于获得与特定数据库连接的日志文件（回滚日志或
预写日志）关联的 sqlite3_file 对象的指针。另见 `SQLITE_FCNTL_FILE_POINTER`。

`SQLITE_FCNTL_SYNC_OMITTED` 文件控制不再使用。

`SQLITE_FCNTL_SYNC` 操作码由 SQLite 内部生成，在对数据库文件描述符调用 xSync 方法
之前立即发送给 VFS。或者，若因用户用 PRAGMA synchronous=OFF 配置 SQLite 而不调用
xSync 方法，则它被用来代替 xSync 方法。多数情况下，随此文件控制传入的指针参数为 NULL。
但若数据库文件正作为多数据库提交的一部分被同步，参数指向包含事务超级日志文件名的
NUL 结尾字符串。不需要此信号的 VFS 应静默忽略此操作码。应用不应以该操作码调用
`sqlite3_file_control()`，因为这样做可能干扰确实需要它的专用 VFS 的操作。

`SQLITE_FCNTL_COMMIT_PHASETWO` 操作码由 SQLite 内部生成，在事务提交后、数据库解锁前
立即发送给 VFS。不需要此信号的 VFS 应静默忽略此操作码。应用不应以该操作码调用
`sqlite3_file_control()`，因为这样做可能干扰确实需要它的专用 VFS 的操作。

`SQLITE_FCNTL_WIN32_AV_RETRY` 操作码用于配置 windows VFS 某些磁盘 I/O 操作的自动
重试次数和间隔，以在存在杀毒程序的情况下提供健壮性。默认情况下，windows VFS 最多
重试文件读、文件写和文件删除操作 10 次，首次重试前延迟 25 毫秒、此后每次重试延迟
增加 25 毫秒。此操作码允许调整这两个值（10 次重试和 25 毫秒延迟）。这些值对同一进程
内所有数据库连接都改变。参数是指向两个整数数组的指针，第一个整数是新重试次数，第二个
整数是延迟。若任一整数为负，则不改设置、而是把该设置的先前值写入数组项，允许查询当前
重试设置。zDbName 参数被忽略。

`SQLITE_FCNTL_PERSIST_WAL` 操作码用于设置或查询持久预写日志设置。默认情况下，辅助
预写日志（WAL 文件）和用于事务控制的共享内存文件在数据库的最新连接关闭时被自动删除。
设置持久 WAL 模式使这些文件在关闭后保留。当其它对包含数据库文件的目录没有写权限的
进程想读取数据库文件时，保留这些文件很有用，因为 WAL 和共享内存文件必须存在数据库
才能被读取。此操作码的 `sqlite3_file_control()` 第四个参数应是指向整数的指针。该整数
为 0 禁用持久 WAL 模式，为 1 启用。若该整数为 -1，则用当前 WAL 持久设置覆盖它。

`SQLITE_FCNTL_POWERSAFE_OVERWRITE` 操作码用于设置或查询持久 "powersafe-overwrite"
（PSOW）设置。PSOW 设置决定 xDeviceCharacteristics 方法的
`SQLITE_IOCAP_POWERSAFE_OVERWRITE` 位。此操作码的 `sqlite3_file_control()` 第四个
参数应是指向整数的指针。该整数为 0 禁用零损坏模式，为 1 启用零损坏模式。若该整数为
-1，则用当前零损坏模式设置覆盖它。

`SQLITE_FCNTL_OVERWRITE` 操作码在打开写事务后由 SQLite 调用，指示：除非事务因某种
原因被回滚，整个数据库文件将被当前事务覆盖。这被 VACUUM 操作使用。

`SQLITE_FCNTL_VFSNAME` 操作码可用于获得 VFS 栈中所有 VFS 的名字。所有 VFS shim 和
最终底层 VFS 的名字写入从 `sqlite3_malloc()` 获得的内存，结果存储在
`sqlite3_file_control()` 第四个参数指向的 char* 变量中。调用者负责用完时释放内存。
与所有文件控制动作一样，不保证它真的会做什么。调用者应把 char* 变量初始化为 NULL
指针，以防此文件控制未实现。此文件控制仅供诊断使用。

`SQLITE_FCNTL_VFS_POINTER` 操作码找到当前使用中的顶层 VFS 的指针。`sqlite3_file_control(db,SQLITE_FCNTL_VFS_POINTER,X)`
中的参数 X 必须为 "sqlite3_vfs **" 类型。此操作码把 *X 设置为指向顶层 VFS 的指针。
当栈中有多个 VFS shim 时，此操作码只找到最上层的 shim。

每当解析 PRAGMA 语句时，会把 `SQLITE_FCNTL_PRAGMA` 文件控制发送到与该 pragma 语句
所指数据库文件对应的打开的 sqlite3_file 对象。`SQLITE_FCNTL_PRAGMA` 文件控制的参数
是一个字符串指针数组（char**），其中数组的第二个元素是 pragma 的名字，第三个元素是
pragma 的参数，若 pragma 无参数则为 NULL。`SQLITE_FCNTL_PRAGMA` 文件控制的处理器可选
地使 char** 参数的第一个元素指向从 `sqlite3_mprintf()` 或等价函数获得的字符串，该
字符串将成为 pragma 的结果、或 pragma 失败时的错误消息。若 `SQLITE_FCNTL_PRAGMA` 文件
控制返回 `SQLITE_NOTFOUND`，则继续正常的 PRAGMA 处理。若返回 `SQLITE_OK`，则解析器
假定 VFS 自己处理了该 PRAGMA：若结果字符串为 NULL，解析器生成空操作的预编译语句；
若字符串非 NULL，则返回结果字符串的副本。若 `SQLITE_FCNTL_PRAGMA` 文件控制返回
`SQLITE_OK` 或 `SQLITE_NOTFOUND` 之外的任何结果码，则表示 VFS 在处理 PRAGMA 时遇到
错误，PRAGMA 的编译以错误失败。`SQLITE_FCNTL_PRAGMA` 文件控制在 pragma 语句分析开始
时发生，因此它能够覆盖内建 PRAGMA 语句。

`SQLITE_FCNTL_BUSYHANDLER` 文件控制可能在数据库文件句柄打开后不久由 SQLite 调用，
为自定义 VFS 提供对连接忙处理器回调的访问。参数类型为 (void**)——两个 (void *) 值的
数组。第一个 (void *) 实际指向类型为 (int (*)(void *)) 的函数。要调用连接的忙处理器，
应以数组中的第二个 (void *) 作为唯一参数调用此函数。若返回非零，则应重试操作；若返回
零，自定义 VFS 应放弃当前操作。

应用可调用 `SQLITE_FCNTL_TEMPFILENAME` 文件控制，让 SQLite 用与生成 TEMP 表及其它
内部用途临时文件名相同的算法生成临时文件名。参数应为 char**，将被填上写入从
`sqlite3_malloc()` 获得的内存的文件名。调用者应对结果调用 `sqlite3_free()` 以避免
内存泄漏。

`SQLITE_FCNTL_MMAP_SIZE` 文件控制用于查询或设置将用于内存映射 I/O 的最大字节数。
参数是指向 sqlite3_int64 类型值的指针，该值是要内存映射的文件字节数的建议最大值。
指针被旧值覆盖。若原来指向的值非负，则不改限制，因此可通过传入指向负数的指针查询当前
限制。此文件控制内部用于实现 PRAGMA mmap_size。

`SQLITE_FCNTL_TRACE` 文件控制向 VFS 提供关于 SQLite 栈更高层在做什么的建议信息。此
文件控制被某些 VFS 活动跟踪 shim 使用。参数是零结尾字符串。若启用 `SQLITE_USE_FCNTL_TRACE`
编译期选项，SQLite 栈的更高层可能生成此文件控制的实例。


`SQLITE_FCNTL_HAS_MOVED` 文件控制把其参数解释为指向整数的指针，并根据文件自首次
打开以来是否被重命名、移动或删除，向该整数写入一个布尔值。

`SQLITE_FCNTL_WIN32_GET_HANDLE` 操作码可用于获得与文件句柄关联的底层本机文件句柄。
此文件控制把其参数解释为指向本机文件句柄的指针，并把结果值写在那里。

`SQLITE_FCNTL_WIN32_SET_HANDLE` 操作码用于调试。此操作码使 xFileControl 方法把文件
句柄与 pArg 参数指向的句柄交换。此能力在测试期间使用，只需在定义 `SQLITE_TEST` 时
支持。

`SQLITE_FCNTL_NULL_IO` 操作码设置 sqlite3_file 对象的底层文件描述符或文件句柄，使其
不再读写数据库文件。

`SQLITE_FCNTL_WAL_BLOCK` 是给 VFS 层的信号：若 WAL 锁不是立即可用，阻塞在下一次 WAL
锁上可能有利。WAL 子系统在罕见情况下发出此信号，以修复优先级反转问题。应用不应使用
此文件控制。

`SQLITE_FCNTL_ZIPVFS` 操作码只由 zipvfs 实现。所有其它 VFS 对此操作码应返回
`SQLITE_NOTFOUND`。

`SQLITE_FCNTL_RBU` 操作码只由 RBU 扩展使用的特殊 VFS 实现。所有其它 VFS 对此操作码
应返回 `SQLITE_NOTFOUND`。

若 `SQLITE_FCNTL_BEGIN_ATOMIC_WRITE` 操作码返回 `SQLITE_OK`，则文件描述符被置于
"批量写模式"，这意味着所有后续写操作将被延迟，在下一次
`SQLITE_FCNTL_COMMIT_ATOMIC_WRITE` 时原子地完成。不支持批量原子写的系统将返回
`SQLITE_NOTFOUND`。在一次成功的 `SQLITE_FCNTL_BEGIN_ATOMIC_WRITE` 之后、到闭合的
`SQLITE_FCNTL_COMMIT_ATOMIC_WRITE` 或 `SQLITE_FCNTL_ROLLBACK_ATOMIC_WRITE` 之前，
SQLite 不会在同一个 sqlite3_file 文件描述符上调用任何 VFS 接口，除了对 xWrite 方法的
调用、以及带 `SQLITE_FCNTL_SIZE_HINT` 的 xFileControl 方法调用。

`SQLITE_FCNTL_COMMIT_ATOMIC_WRITE` 操作码使自上一次成功调用
`SQLITE_FCNTL_BEGIN_ATOMIC_WRITE` 以来的所有写操作被原子地执行。当且仅当所有写操作
都成功完成并已提交到持久存储时，此文件控制返回 `SQLITE_OK`。无论成功与否，此文件控制
都把文件描述符移出批量写模式，使所有后续写操作独立。SQLite 绝不会在先前没有成功调用
`SQLITE_FCNTL_BEGIN_ATOMIC_WRITE` 的情况下调用 `SQLITE_FCNTL_COMMIT_ATOMIC_WRITE`。

`SQLITE_FCNTL_ROLLBACK_ATOMIC_WRITE` 操作码使自上一次成功调用
`SQLITE_FCNTL_BEGIN_ATOMIC_WRITE` 以来的所有写操作被回滚。此文件控制把文件描述符移出
批量写模式，使所有后续写操作独立。SQLite 绝不会在先前没有成功调用
`SQLITE_FCNTL_BEGIN_ATOMIC_WRITE` 的情况下调用 `SQLITE_FCNTL_ROLLBACK_ATOMIC_WRITE`。

`SQLITE_FCNTL_LOCK_TIMEOUT` 操作码用于配置 VFS：在尝试用 VFS 的 xLock 或 xShmLock
方法取得文件锁失败前，最多阻塞 M 毫秒。参数是指向包含 M 要设为的值的 32 位有符号整数
的指针。返回前，该 32 位有符号整数被 M 的先前值覆盖。

`SQLITE_FCNTL_BLOCK_ON_CONNECT` 操作码用于配置 VFS：在取 SHARED 锁连接 wal 模式数据库
时阻塞。这用于实现与 `SQLITE_SETLK_BLOCK_ON_CONNECT` 关联的功能。

`SQLITE_FCNTL_DATA_VERSION` 操作码用于检测数据库文件的更改。参数是指向 32 位无符号
整数的指针。pager 的 "data version" 被写入该指针。"data version" 在对应数据库文件发生
任何更改时改变，无论更改是通过同一数据库连接上的 SQL 语句、还是通过可能位于其它进程的
独立数据库连接提交的事务。`sqlite3_total_changes()` 接口可用于发现连接上是否有任何
数据库已更改，但该接口对 TEMP 和 MAIN 都响应、且不提供只检测 MAIN 更改的机制。此外，
`sqlite3_total_changes()` 接口只响应内部更改、省略其它数据库连接所做的更改。PRAGMA
data_version 命令提供检测单个附加数据库因其它数据库连接而发生的更改的机制，但省略由
调用它的数据库连接实现的更改。此文件控制是检测内部或外部发生的、与特定附加数据库关联
的更改的唯一机制。

`SQLITE_FCNTL_CKPT_START` 操作码在 wal 模式检查点内、客户端开始把页从 wal 文件复制到
数据库文件之前调用。

`SQLITE_FCNTL_CKPT_DONE` 操作码在 wal 模式检查点内、客户端完成把页从 wal 文件复制到
数据库文件之后、但 *-shm 文件更新以记录这些页已被检查点之前调用。

实验性的 `SQLITE_FCNTL_EXTERNAL_READER` 操作码用于检测另一进程中是否有数据库客户端
在该数据库上打开 wal 模式事务。它只在 unix 上可用。随此文件控制传入的 (void*) 参数
应是指向 (int) 类型值的指针。若数据库是 wal 模式数据库、且其它进程中至少有一个客户端
当前在该数据库上打开 SQL 事务，则该整数值被设为 1。若数据库不是 wal 模式数据库、或
任何其它进程中没有这样的连接，则被设为 0。此操作码不能用于检测当前进程内客户端打开
的事务，只能检测其它进程中的。

`SQLITE_FCNTL_CKSM_FILE` 操作码仅供校验和 VFS shim 内部使用。

若数据库上当前没有打开事务、且数据库不是临时数据库，则 `SQLITE_FCNTL_RESET_CACHE`
文件控制清除内存页缓存的内容。若有打开的事务、或数据库是临时数据库，此操作码是空操作，
而非错误。

`SQLITE_FCNTL_FILESTAT` 操作码返回关于访问给定 schema 的数据库和日志文件所用的
sqlite3_file 对象的底层诊断信息。`sqlite3_file_control()` 的第四个参数应是已初始化的
sqlite3_str 指针。描述 sqlite3_file 对象各个方面的 JSON 文本被追加到 sqlite3_str。
`SQLITE_FCNTL_FILESTAT` 操作码通常是空操作，除非用编译期选项启用它。

## 虚拟表约束算子代码（Virtual Table Constraint Operator Codes）

```
#define SQLITE_INDEX_CONSTRAINT_EQ          2
#define SQLITE_INDEX_CONSTRAINT_GT          4
#define SQLITE_INDEX_CONSTRAINT_LE          8
#define SQLITE_INDEX_CONSTRAINT_LT         16
#define SQLITE_INDEX_CONSTRAINT_GE         32
#define SQLITE_INDEX_CONSTRAINT_MATCH      64
#define SQLITE_INDEX_CONSTRAINT_LIKE       65
#define SQLITE_INDEX_CONSTRAINT_GLOB       66
#define SQLITE_INDEX_CONSTRAINT_REGEXP     67
#define SQLITE_INDEX_CONSTRAINT_NE         68
#define SQLITE_INDEX_CONSTRAINT_ISNOT      69
#define SQLITE_INDEX_CONSTRAINT_ISNOTNULL  70
#define SQLITE_INDEX_CONSTRAINT_ISNULL     71
#define SQLITE_INDEX_CONSTRAINT_IS         72
#define SQLITE_INDEX_CONSTRAINT_LIMIT      73
#define SQLITE_INDEX_CONSTRAINT_OFFSET     74
#define SQLITE_INDEX_CONSTRAINT_FUNCTION  150
```

这些宏定义 sqlite3_index_info.aConstraint[].op 字段的允许值。每个值表示使用虚拟表的
查询的 WHERE 子句中约束项的一部分算子。

算子的左侧操作数由对应的 aConstraint[].iColumn 字段给出。iColumn 为 -1 表示左侧操作数
是 rowid。`SQLITE_INDEX_CONSTRAINT_LIMIT` 和 `SQLITE_INDEX_CONSTRAINT_OFFSET` 算子没有
左侧操作数，因此对这些算子，对应的 aConstraint[].iColumn 无意义、不应使用。

从 `SQLITE_INDEX_CONSTRAINT_FUNCTION` 到值 255 的所有算子值保留用于表示由虚拟表实现的
xFindFunction 方法重载的函数。

每个约束的右侧操作数可能可用 `sqlite3_vtab_rhs_value()` 接口访问。通常右侧操作数只在
它作为输入 SQL 中的单个常量字面量出现时可用。若右侧操作数是另一列、表达式（即使是
常量表达式）或参数，则 `sqlite3_vtab_rhs_value()` 很可能无法提取它。
`SQLITE_INDEX_CONSTRAINT_ISNULL` 和 `SQLITE_INDEX_CONSTRAINT_ISNOTNULL` 算子没有右侧
操作数，因此对这些算子调用 `sqlite3_vtab_rhs_value()` 将总是返回 `SQLITE_NOTFOUND`。

用于比较的排序规则可用 `sqlite3_vtab_collation()` 接口找到。对大多数现实世界的虚拟表，
约束的排序规则无关紧要（例如因为约束是数字的），因此 `sqlite3_vtab_collation()` 接口
不常需要。

## 虚拟表扫描标志（Virtual Table Scan Flags）

```
#define SQLITE_INDEX_SCAN_UNIQUE 0x00000001 /* 扫描最多访问 1 行 */
#define SQLITE_INDEX_SCAN_HEX    0x00000002 /* 在 EXPLAIN QUERY PLAN 中把 idxNum 显示为十六进制 */
```

允许虚拟表实现把 sqlite3_index_info.idxFlags 字段设置为这些位的某种组合。

## 设备特性（Device Characteristics）

```
#define SQLITE_IOCAP_ATOMIC                 0x00000001
#define SQLITE_IOCAP_ATOMIC512              0x00000002
#define SQLITE_IOCAP_ATOMIC1K               0x00000004
#define SQLITE_IOCAP_ATOMIC2K               0x00000008
#define SQLITE_IOCAP_ATOMIC4K               0x00000010
#define SQLITE_IOCAP_ATOMIC8K               0x00000020
#define SQLITE_IOCAP_ATOMIC16K              0x00000040
#define SQLITE_IOCAP_ATOMIC32K              0x00000080
#define SQLITE_IOCAP_ATOMIC64K              0x00000100
#define SQLITE_IOCAP_SAFE_APPEND            0x00000200
#define SQLITE_IOCAP_SEQUENTIAL             0x00000400
#define SQLITE_IOCAP_UNDELETABLE_WHEN_OPEN  0x00000800
#define SQLITE_IOCAP_POWERSAFE_OVERWRITE    0x00001000
#define SQLITE_IOCAP_IMMUTABLE              0x00002000
#define SQLITE_IOCAP_BATCH_ATOMIC           0x00004000
#define SQLITE_IOCAP_SUBPAGE_READ           0x00008000
```

sqlite3_io_methods 对象的 xDeviceCharacteristics 方法返回一个整数，它是这些位值的
向量，表示持有 sqlite3_io_methods 所指文件的存储设备的 I/O 特性。

`SQLITE_IOCAP_ATOMIC` 特性表示任意大小的写都是原子的。`SQLITE_IOCAP_ATOMICnnn` 值
表示：大小为 nnn 字节、且对齐到 nnn 整数倍地址的整块写是原子的。`SQLITE_IOCAP_SAFE_APPEND`
值表示：向文件追加数据时，先追加数据、再扩展文件大小，绝不会反过来。
`SQLITE_IOCAP_SEQUENTIAL` 特性表示信息按 xWrite() 调用的顺序写入磁盘。
`SQLITE_IOCAP_POWERSAFE_OVERWRITE` 特性表示：崩溃或断电后重启，文件中只有应用层写入
的字节可能改变，相邻字节（即使同一扇区内的字节）保证不变。`SQLITE_IOCAP_UNDELETABLE_WHEN_OPEN`
标志表示文件打开时不能被删除。`SQLITE_IOCAP_IMMUTABLE` 标志表示文件位于只读介质上，
即使有提升权限的进程也不能改变。

`SQLITE_IOCAP_BATCH_ATOMIC` 特性表示底层文件系统支持原子地完成多个写操作，当这些写
操作被 `SQLITE_FCNTL_BEGIN_ATOMIC_WRITE` 和 `SQLITE_FCNTL_COMMIT_ATOMIC_WRITE` 括起时。

`SQLITE_IOCAP_SUBPAGE_READ` 特性表示以不是页大小整数倍的量、且不从页边界开始的量读取
数据库文件是可以的。没有此特性，SQLite 谨慎地只做对齐页上的整页读写，唯一例外是读第一
页的次页访问数据库头。

## 文件锁级别（File Locking Levels）

```
#define SQLITE_LOCK_NONE          0       /* 仅 xUnlock() */
#define SQLITE_LOCK_SHARED        1       /* xLock() 或 xUnlock() */
#define SQLITE_LOCK_RESERVED      2       /* 仅 xLock() */
#define SQLITE_LOCK_PENDING       3       /* 仅 xLock() */
#define SQLITE_LOCK_EXCLUSIVE     4       /* 仅 xLock() */
```

SQLite 用这些整数值之一作为它对 sqlite3_io_methods 对象的 xLock() 和 xUnlock() 方法
调用的第二个参数。这些值从最不严格到最严格排序。

xLock() 的参数总是 SHARED 或更高。xUnlock 的参数要么是 SHARED、要么是 NONE。

## 互斥类型（Mutex Types）

```
#define SQLITE_MUTEX_FAST             0
#define SQLITE_MUTEX_RECURSIVE        1
#define SQLITE_MUTEX_STATIC_MAIN      2
#define SQLITE_MUTEX_STATIC_MEM       3  /* sqlite3_malloc() */
#define SQLITE_MUTEX_STATIC_MEM2      4  /* 未使用 */
#define SQLITE_MUTEX_STATIC_OPEN      4  /* sqlite3BtreeOpen() */
#define SQLITE_MUTEX_STATIC_PRNG      5  /* sqlite3_randomness() */
#define SQLITE_MUTEX_STATIC_LRU       6  /* lru 页列表 */
#define SQLITE_MUTEX_STATIC_LRU2      7  /* 未使用 */
#define SQLITE_MUTEX_STATIC_PMEM      7  /* sqlite3PageMalloc() */
#define SQLITE_MUTEX_STATIC_APP1      8  /* 供应用使用 */

#define SQLITE_MUTEX_STATIC_APP2      9  /* 供应用使用 */
#define SQLITE_MUTEX_STATIC_APP3     10  /* 供应用使用 */
#define SQLITE_MUTEX_STATIC_VFS1     11  /* 供内建 VFS 使用 */
#define SQLITE_MUTEX_STATIC_VFS2     12  /* 供扩展 VFS 使用 */
#define SQLITE_MUTEX_STATIC_VFS3     13  /* 供应用 VFS 使用 */
```

`sqlite3_mutex_alloc()` 接口接受单个参数，即这些整数常量之一。

静态互斥锁的集合可能随 SQLite 版本而变。覆盖内建互斥锁逻辑的应用必须准备好容纳额外的
静态互斥锁。

## 文件打开操作标志（Flags For File Open Operations）

```
#define SQLITE_OPEN_READONLY         0x00000001  /* 可用于 sqlite3_open_v2() */
#define SQLITE_OPEN_READWRITE        0x00000002  /* 可用于 sqlite3_open_v2() */
#define SQLITE_OPEN_CREATE           0x00000004  /* 可用于 sqlite3_open_v2() */
#define SQLITE_OPEN_DELETEONCLOSE    0x00000008  /* 仅 VFS */
#define SQLITE_OPEN_EXCLUSIVE        0x00000010  /* 仅 VFS */
#define SQLITE_OPEN_AUTOPROXY        0x00000020  /* 仅 VFS */
#define SQLITE_OPEN_URI              0x00000040  /* 可用于 sqlite3_open_v2() */
#define SQLITE_OPEN_MEMORY           0x00000080  /* 可用于 sqlite3_open_v2() */
#define SQLITE_OPEN_MAIN_DB          0x00000100  /* 仅 VFS */
#define SQLITE_OPEN_TEMP_DB          0x00000200  /* 仅 VFS */
#define SQLITE_OPEN_TRANSIENT_DB     0x00000400  /* 仅 VFS */
#define SQLITE_OPEN_MAIN_JOURNAL     0x00000800  /* 仅 VFS */
#define SQLITE_OPEN_TEMP_JOURNAL     0x00001000  /* 仅 VFS */
#define SQLITE_OPEN_SUBJOURNAL       0x00002000  /* 仅 VFS */
#define SQLITE_OPEN_SUPER_JOURNAL    0x00004000  /* 仅 VFS */
#define SQLITE_OPEN_NOMUTEX          0x00008000  /* 可用于 sqlite3_open_v2() */
#define SQLITE_OPEN_FULLMUTEX        0x00010000  /* 可用于 sqlite3_open_v2() */
#define SQLITE_OPEN_SHAREDCACHE      0x00020000  /* 可用于 sqlite3_open_v2() */
#define SQLITE_OPEN_PRIVATECACHE     0x00040000  /* 可用于 sqlite3_open_v2() */
#define SQLITE_OPEN_WAL              0x00080000  /* 仅 VFS */
#define SQLITE_OPEN_NOFOLLOW         0x01000000  /* 可用于 sqlite3_open_v2() */
#define SQLITE_OPEN_EXRESCODE        0x02000000  /* 扩展结果码 */
```

这些位值用于 `sqlite3_open_v2()` 接口的第三个参数和 `sqlite3_vfs.xOpen` 方法的第四个
参数。

只有标记为 "可用于 sqlite3_open_v2()" 的标志才能用作 `sqlite3_open_v2()` 接口的第三个
参数。历史上 `sqlite3_open_v2()` 忽略其它标志，但未来版本的 SQLite 可能改变：若把任何
不允许的位传入 `sqlite3_open_v2()` 则报错。应用不应依赖历史行为。

特别注意：把 `SQLITE_OPEN_EXCLUSIVE` 标志传入 `sqlite3_open_v2()` 并不导致底层数据库
文件用 O_EXCL 打开。历史上把 `SQLITE_OPEN_EXCLUSIVE` 传入 `sqlite3_open_v2()` 是空操作，
未来版本的 SQLite 可能使其成为错误。

## 预编译标志（Prepare Flags）

```
#define SQLITE_PREPARE_PERSISTENT              0x01
#define SQLITE_PREPARE_NORMALIZE               0x02
#define SQLITE_PREPARE_NO_VTAB                 0x04
#define SQLITE_PREPARE_DONT_LOG                0x10
#define SQLITE_PREPARE_FROM_DDL                0x20
```

这些常量定义可传入 `sqlite3_prepare_v3()` 和 `sqlite3_prepare16_v3()` 接口的 "prepFlags"
参数的各种标志。

未来版本的 SQLite 可能加入新标志。

**SQLITE_PREPARE_PERSISTENT**：此标志是给查询规划器的提示：预编译语句将被长期保留、
很可能被多次复用。没有此标志，`sqlite3_prepare_v3()` 和 `sqlite3_prepare16_v3()` 假定
预编译语句只被使用一次或最多几次，然后相对较快地用 `sqlite3_finalize()` 销毁。当前实现
通过避免使用 lookaside 内存来响应此提示，以免耗尽有限的 lookaside 内存存储。未来版本的
SQLite 可能以不同方式响应此提示。

**SQLITE_PREPARE_NORMALIZE**：此标志是空操作。此标志曾经是任何想使用
`sqlite3_normalized_sql()` 接口的预编译语句所必需的。但现在 `sqlite3_normalized_sql()`
接口对所有预编译语句都可用，无论是否使用此标志。

**SQLITE_PREPARE_NO_VTAB**：此标志使 SQL 编译器在语句使用任何虚拟表时返回错误（错误码
`SQLITE_ERROR`）。

**SQLITE_PREPARE_DONT_LOG**：此标志阻止 SQL 编译器错误被发送到 `SQLITE_CONFIG_LOG`
定义的错误日志。例如可用于做测试编译，检查某些 SQL 语法是否良好，而不在不良好时在全局
错误日志上生成消息。若测试编译失败，无论有无此标志，`sqlite3_prepare_v3()` 调用都返回
相同的错误指示；它只是省略记录错误的 `sqlite3_log()` 调用。

**SQLITE_PREPARE_FROM_DDL**：此标志使 SQL 编译器强制实施那些否则只在解析数据库 schema
时才强制实施的安全约束。换句话说，此标志使 SQL 编译器把正在准备的 SQL 语句当作来自
攻击者。使用 `SQLITE_PREPARE_FROM_DDL` 且 `SQLITE_DBCONFIG_TRUSTED_SCHEMA` 关闭时，
SQL 函数只有在被标记为 `SQLITE_INNOCUOUS` 时才能被调用，虚拟表只有在被标记为
`SQLITE_VTAB_INNOCUOUS` 时才能被使用。最佳实践：准备任何派生自数据库 schema 一部分的
SQL 时使用 `SQLITE_PREPARE_FROM_DDL` 选项。尤其是，运行派生自其 CREATE VIRTUAL TABLE
语句参数的 SQL 语句的虚拟表实现，应总是使用 `sqlite3_prepare_v3()` 并设置
`SQLITE_PREPARE_FROM_DDL` 标志，以防止绕过 `SQLITE_DBCONFIG_TRUSTED_SCHEMA` 安全检查。

## 预编译语句扫描状态（Prepared Statement Scan Status）

```
#define SQLITE_SCANSTAT_COMPLEX 0x0001
```

## 预编译语句扫描状态操作码（Prepared Statement Scan Status Opcodes）

```
#define SQLITE_SCANSTAT_NLOOP    0
#define SQLITE_SCANSTAT_NVISIT   1
#define SQLITE_SCANSTAT_EST      2
#define SQLITE_SCANSTAT_NAME     3
#define SQLITE_SCANSTAT_EXPLAIN  4
#define SQLITE_SCANSTAT_SELECTID 5
#define SQLITE_SCANSTAT_PARENTID 6
#define SQLITE_SCANSTAT_NCYCLE   7
```

以下常量可用于 `sqlite3_stmt_scanstatus(S,X,T,V)` 接口的 T 参数。每个常量指定
`sqlite3_stmt_scanstatus()` 要返回的一种不同度量。

当返回给 V 的值是字符串时，容纳该字符串的空间由预编译语句 S 管理，S 被终结时自动释放。

并非所有值对所有查询元素都可用。当某值不可用时，若该值是数值则输出变量被设为 -1，若是
字符串（`SQLITE_SCANSTAT_NAME`）则设为 NULL。

**SQLITE_SCANSTAT_NLOOP**：V 参数指向的 sqlite3_int64 变量将被设为第 X 个循环运行的总
次数。

**SQLITE_SCANSTAT_NVISIT**：V 参数指向的 sqlite3_int64 变量将被设为第 X 个循环所有
迭代检查的行总数。

**SQLITE_SCANSTAT_EST**：V 参数指向的 "double" 变量将被设为查询规划器对第 X 个循环
每次迭代输出的平均行数的估计。若查询规划器的估计准确，则此值将近似于 NVISIT/NLOOP
的商，且对同一 SELECTID 的所有先前循环此值的乘积将是当前循环的 NLOOP 值。

**SQLITE_SCANSTAT_NAME**：V 参数指向的 "const char *" 变量将被设为包含第 X 个循环所用
索引或表名的零结尾 UTF-8 字符串。

**SQLITE_SCANSTAT_EXPLAIN**：V 参数指向的 "const char *" 变量将被设为包含第 X 个循环
的 EXPLAIN QUERY PLAN 描述的零结尾 UTF-8 字符串。

**SQLITE_SCANSTAT_SELECTID**：V 参数指向的 "int" 变量将被设为第 X 个查询计划元素的
id。该 id 值在语句内唯一。select-id 与 EXPLAIN QUERY PLAN 查询第一列输出的值相同。

**SQLITE_SCANSTAT_PARENTID**：V 参数指向的 "int" 变量将被设为当前查询元素的父元素 id
（若适用），若查询元素没有父元素则设为零。这与 EXPLAIN QUERY PLAN 查询第二列返回的值
相同。

**SQLITE_SCANSTAT_NCYCLE**：sqlite3_int64 输出值被设为处理查询元素期间、根据处理器
时间戳计数器流逝的周期数。此值并非对所有查询元素都可用——若不可用，输出变量被设为 -1。

## 编译期库版本号（Compile-Time Library Version Numbers）

```
#define SQLITE_VERSION        "3.53.4"
#define SQLITE_VERSION_NUMBER 3053004
#define SQLITE_SOURCE_ID      "2026-07-24 19:02:57 bf7c7f30031888f4e796e429ab3978879485813aaca6f641c7b33e4e09459bcc"
#define SQLITE_SCM_BRANCH     "branch-3.53"
#define SQLITE_SCM_TAGS       "release version-3.53.4"
#define SQLITE_SCM_DATETIME   "2026-07-24T19:02:57.525Z"
```

sqlite3.h 头文件中的 `SQLITE_VERSION` C 预处理宏求值为字符串字面量，是格式 "X.Y.Z" 的
SQLite 版本，其中 X 是主版本号（对 SQLite3 总是 3）、Y 是次版本号、Z 是发布号。
`SQLITE_VERSION_NUMBER` C 预处理宏求值为值 (X*1000000 + Y*1000 + Z) 的整数，其中 X、Y、
Z 与 `SQLITE_VERSION` 中使用的数字相同。任何给定 SQLite 版本的 `SQLITE_VERSION_NUMBER`
也将大于其派生自的版本。要么 Y 保持不变且 Z 递增，要么 Y 递增且 Z 重置为零。

自版本 3.6.18（2009-09-11）起，SQLite 源代码存储在 Fossil 配置管理系统中。
`SQLITE_SOURCE_ID` 宏求值为一个字符串，标识其配置管理系统中 SQLite 的特定 check-in。
`SQLITE_SOURCE_ID` 字符串包含 check-in 的日期时间（UTC）和整个源代码树的 SHA1 或
SHA3-256 哈希。若源代码自上次 check-in 以来以任何方式被编辑，则哈希的最后四个十六进制
数字可能被修改。

另见：sqlite3_libversion()、sqlite3_libversion_number()、sqlite3_sourceid()、
sqlite_version() 和 sqlite_source_id()。

## xShmLock VFS 方法的标志（Flags for the xShmLock VFS method）

```
#define SQLITE_SHM_UNLOCK       1
#define SQLITE_SHM_LOCK         2
#define SQLITE_SHM_SHARED       4
#define SQLITE_SHM_EXCLUSIVE    8
```

这些整数常量定义 sqlite3_io_methods 的 xShmLock 方法允许的各种锁操作。以下是传给
xShmLock 方法的唯一合法标志组合：

- `SQLITE_SHM_LOCK | SQLITE_SHM_SHARED`
- `SQLITE_SHM_LOCK | SQLITE_SHM_EXCLUSIVE`
- `SQLITE_SHM_UNLOCK | SQLITE_SHM_SHARED`
- `SQLITE_SHM_UNLOCK | SQLITE_SHM_EXCLUSIVE`

解锁时，必须提供与加锁时相同的 SHARED 或 EXCLUSIVE 标志。

xShmLock 方法可以在未锁定与 SHARED 之间、或未锁定与 EXCLUSIVE 之间转换。它不能在
SHARED 与 EXCLUSIVE 之间转换。

## 定义特殊析构函数行为的常量（Constants Defining Special Destructor Behavior）

```
typedef void (*sqlite3_destructor_type)(void*);
#define SQLITE_STATIC      ((sqlite3_destructor_type)0)
#define SQLITE_TRANSIENT   ((sqlite3_destructor_type)-1)
```

这些是作为最终参数传给 `sqlite3_result_blob()` 等例程的析构函数的特殊值。若析构函数
参数是 `SQLITE_STATIC`，表示内容指针是常量、永不改变，不需要被销毁。`SQLITE_TRANSIENT`
值表示内容很可能在不久将来改变，SQLite 应在返回前制作内容的私有副本。

该 typedef 为规避某些 C++ 编译器中的问题所必需。

## 状态参数（Status Parameters）


```
#define SQLITE_STATUS_MEMORY_USED          0
#define SQLITE_STATUS_PAGECACHE_USED       1
#define SQLITE_STATUS_PAGECACHE_OVERFLOW   2
#define SQLITE_STATUS_SCRATCH_USED         3  /* 未使用 */
#define SQLITE_STATUS_SCRATCH_OVERFLOW     4  /* 未使用 */
#define SQLITE_STATUS_MALLOC_SIZE          5
#define SQLITE_STATUS_PARSER_STACK         6
#define SQLITE_STATUS_PAGECACHE_SIZE       7
#define SQLITE_STATUS_SCRATCH_SIZE         8  /* 未使用 */
#define SQLITE_STATUS_MALLOC_COUNT         9
```

这些整数常量指定 `sqlite3_status()` 可以返回的各种运行时状态参数。

**SQLITE_STATUS_MEMORY_USED**：此参数是当前用 `sqlite3_malloc()` 直接或间接借出的
内存量。该数字包括应用对 `sqlite3_malloc()` 的调用、以及 SQLite 库的内部内存使用。
`SQLITE_CONFIG_PAGECACHE` 控制的辅助页缓存内存不包含在此参数中。返回的量是
sqlite3_mem_methods 中 xSize 方法报告的各分配大小的总和。

**SQLITE_STATUS_MALLOC_SIZE**：此参数记录交给 `sqlite3_malloc()` 或
`sqlite3_realloc()`（或其内部等价物）的最大内存分配请求。只有 `sqlite3_status()` 的
*pHighwater 参数返回的值有意义。写入 *pCurrent 参数的值未定义。

**SQLITE_STATUS_MALLOC_COUNT**：此参数记录当前借出的独立内存分配数。

**SQLITE_STATUS_PAGECACHE_USED**：此参数返回用 `SQLITE_CONFIG_PAGECACHE` 配置的
页缓存内存分配器中已使用的页数。返回的值以页为单位，而非字节。

**SQLITE_STATUS_PAGECACHE_OVERFLOW**：此参数返回无法由 `SQLITE_CONFIG_PAGECACHE`
缓冲区满足、被迫溢出到 `sqlite3_malloc()` 的页缓存分配的字节数。返回值包括因太大而
溢出（大于 `SQLITE_CONFIG_PAGECACHE` 的 "sz" 参数）的分配、以及因页缓存中没有剩余
空间而溢出的分配。

**SQLITE_STATUS_PAGECACHE_SIZE**：此参数记录交给页缓存内存分配器的最大内存分配
请求。只有 `sqlite3_status()` 的 *pHighwater 参数返回的值有意义。写入 *pCurrent 参数
的值未定义。

**SQLITE_STATUS_SCRATCH_USED**：不再使用。

**SQLITE_STATUS_SCRATCH_OVERFLOW**：不再使用。

**SQLITE_STATUS_SCRATCH_SIZE**：不再使用。

**SQLITE_STATUS_PARSER_STACK**：*pHighwater 参数记录最深的解析器栈。*pCurrent 值
未定义。*pHighwater 值仅在 SQLite 用 YYTRACKMAXSTACKDEPTH 编译时有意义。

可能不时加入新的状态参数。

## 同步类型标志（Synchronization Type Flags）

```
#define SQLITE_SYNC_NORMAL        0x00002
#define SQLITE_SYNC_FULL          0x00003
#define SQLITE_SYNC_DATAONLY      0x00010
```

当 SQLite 调用 sqlite3_io_methods 对象的 xSync() 方法时，它用这些整数值的组合作为
第二个参数。

使用 `SQLITE_SYNC_DATAONLY` 标志时，表示同步操作只需把数据刷到海量存储，inode 信息
不必刷。若标志的低四位等于 `SQLITE_SYNC_NORMAL`，表示使用普通 fsync() 语义。若低四位
等于 `SQLITE_SYNC_FULL`，表示用 Mac OS X 风格的 fullsync 代替 fsync()。

不要把 `SQLITE_SYNC_NORMAL` 和 `SQLITE_SYNC_FULL` 标志与 PRAGMA synchronous=NORMAL
和 PRAGMA synchronous=FULL 设置混淆。synchronous pragma 决定 xSync VFS 方法的调用
何时发生，并在所有平台上统一适用。`SQLITE_SYNC_NORMAL` 和 `SQLITE_SYNC_FULL` 标志
决定同步操作的力度或严格程度，对默认 SQLite 代码只在 Mac OSX 上产生影响。（第三方 VFS
实现也可能区分 `SQLITE_SYNC_NORMAL` 和 `SQLITE_SYNC_FULL`，但在 SQLite 原生支持的
操作系统中，只有 Mac OSX 关心这个区别。）

## 测试接口操作码（Testing Interface Operation Codes）

```
#define SQLITE_TESTCTRL_FIRST                    5
#define SQLITE_TESTCTRL_PRNG_SAVE                5
#define SQLITE_TESTCTRL_PRNG_RESTORE             6
#define SQLITE_TESTCTRL_PRNG_RESET               7  /* 未使用 */
#define SQLITE_TESTCTRL_FK_NO_ACTION             7
#define SQLITE_TESTCTRL_BITVEC_TEST              8
#define SQLITE_TESTCTRL_FAULT_INSTALL            9
#define SQLITE_TESTCTRL_BENIGN_MALLOC_HOOKS     10
#define SQLITE_TESTCTRL_PENDING_BYTE            11
#define SQLITE_TESTCTRL_ASSERT                  12
#define SQLITE_TESTCTRL_ALWAYS                  13
#define SQLITE_TESTCTRL_RESERVE                 14  /* 未使用 */
#define SQLITE_TESTCTRL_JSON_SELFCHECK          14
#define SQLITE_TESTCTRL_OPTIMIZATIONS           15
#define SQLITE_TESTCTRL_ISKEYWORD               16  /* 未使用 */
#define SQLITE_TESTCTRL_GETOPT                  16
#define SQLITE_TESTCTRL_SCRATCHMALLOC           17  /* 未使用 */
#define SQLITE_TESTCTRL_INTERNAL_FUNCTIONS      17
#define SQLITE_TESTCTRL_LOCALTIME_FAULT         18
#define SQLITE_TESTCTRL_EXPLAIN_STMT            19  /* 未使用 */
#define SQLITE_TESTCTRL_ONCE_RESET_THRESHOLD    19
#define SQLITE_TESTCTRL_NEVER_CORRUPT           20
#define SQLITE_TESTCTRL_VDBE_COVERAGE           21
#define SQLITE_TESTCTRL_BYTEORDER               22
#define SQLITE_TESTCTRL_ISINIT                  23
#define SQLITE_TESTCTRL_SORTER_MMAP             24
#define SQLITE_TESTCTRL_IMPOSTER                25
#define SQLITE_TESTCTRL_PARSER_COVERAGE         26
#define SQLITE_TESTCTRL_RESULT_INTREAL          27
#define SQLITE_TESTCTRL_PRNG_SEED               28
#define SQLITE_TESTCTRL_EXTRA_SCHEMA_CHECKS     29
#define SQLITE_TESTCTRL_SEEK_COUNT              30
#define SQLITE_TESTCTRL_TRACEFLAGS              31
#define SQLITE_TESTCTRL_TUNE                    32
#define SQLITE_TESTCTRL_LOGEST                  33
#define SQLITE_TESTCTRL_USELONGDOUBLE           34  /* 未使用 */
#define SQLITE_TESTCTRL_ATOF                    34
#define SQLITE_TESTCTRL_LAST                    34  /* 最大的 TESTCTRL */
```

这些常量是 `sqlite3_test_control()` 第一个参数使用的有效操作码参数。

这些参数及其含义可能随时更改而不通知。这些值仅供测试使用。应用不应使用任何这些参数或
`sqlite3_test_control()` 接口。

## SQL 跟踪事件码（SQL Trace Event Codes）

```
#define SQLITE_TRACE_STMT       0x01
#define SQLITE_TRACE_PROFILE    0x02
#define SQLITE_TRACE_ROW        0x04
#define SQLITE_TRACE_CLOSE      0x08
```

这些常量标识可用 `sqlite3_trace_v2()` 跟踪逻辑监控的事件类别。`sqlite3_trace_v2(D,M,X,P)`
的 M 参数是一个或多个以下常量的 OR 组合。跟踪回调的第一个参数是以下常量之一。

未来版本可能加入新的跟踪常量。

跟踪回调有四个参数：xCallback(T,C,P,X)。T 参数是上面的整数类型码之一。C 参数是作为
`sqlite3_trace_v2()` 第四个参数传入的上下文指针的副本。P 和 X 参数是指针，其含义取决
于 T。

**SQLITE_TRACE_STMT**：当预编译语句第一次开始运行时、以及可能在预编译语句执行期间
的其它时刻（例如每个触发器子程序开始时）调用 `SQLITE_TRACE_STMT` 回调。P 参数是指向
预编译语句的指针。X 参数是指向字符串的指针，该字符串是预编译语句的未展开 SQL 文本、
或指示触发器调用的 SQL 注释。回调可用 X 参数计算传统 `sqlite3_trace()` 接口会返回的
相同文本：当 X 以 "--" 开头时用 X，否则调用 `sqlite3_expanded_sql(P)`。

**SQLITE_TRACE_PROFILE**：`SQLITE_TRACE_PROFILE` 回调提供与 `sqlite3_profile()` 回调
大致相同的信息。P 参数是指向预编译语句的指针，X 参数指向一个 64 位整数，近似于预编译
语句运行所花的纳秒数。语句完成时调用 `SQLITE_TRACE_PROFILE` 回调。

**SQLITE_TRACE_ROW**：每当预编译语句生成单个结果行时调用 `SQLITE_TRACE_ROW` 回调。
P 参数是指向预编译语句的指针，X 参数未使用。

**SQLITE_TRACE_CLOSE**：数据库连接关闭时调用 `SQLITE_TRACE_CLOSE` 回调。P 参数是指向
数据库连接对象的指针，X 参数未使用。

## sqlite3_txn_state() 的允许返回值（Allowed return values from sqlite3_txn_state()）

```
#define SQLITE_TXN_NONE  0
#define SQLITE_TXN_READ  1
#define SQLITE_TXN_WRITE 2
```

这些常量定义数据库文件的当前事务状态。`sqlite3_txn_state(D,S)` 接口返回这些常量之一，
以描述数据库连接 D 中 schema S 的事务状态。

**SQLITE_TXN_NONE**：`SQLITE_TXN_NONE` 状态表示当前没有待处理的事务。

**SQLITE_TXN_READ**：`SQLITE_TXN_READ` 状态表示数据库当前处于读事务中。已从数据库文件
读取内容，但数据库文件中没有任何改变。若有任何更改发生、且没有其它冲突的并发写事务，
事务状态将推进到 `SQLITE_TXN_WRITE`。在 ROLLBACK 或 COMMIT 之后，事务状态将恢复到
`SQLITE_TXN_NONE`。

**SQLITE_TXN_WRITE**：`SQLITE_TXN_WRITE` 状态表示数据库当前处于写事务中。内容已写入
数据库文件但尚未提交。事务状态将在下一次 ROLLBACK 或 COMMIT 时变为 `SQLITE_TXN_NONE`。

## 虚拟表配置选项（Virtual Table Configuration Options）

```
#define SQLITE_VTAB_CONSTRAINT_SUPPORT 1
#define SQLITE_VTAB_INNOCUOUS          2
#define SQLITE_VTAB_DIRECTONLY         3
#define SQLITE_VTAB_USES_ALL_SCHEMAS   4
```

这些宏定义 `sqlite3_vtab_config()` 接口的各种选项，虚拟表实现可用它们自定义和优化其
行为。

**SQLITE_VTAB_CONSTRAINT_SUPPORT**：支持 `sqlite3_vtab_config(db,SQLITE_VTAB_CONSTRAINT_SUPPORT,X)`
形式的调用，其中 X 是整数。若 X 为零，则调用 `sqlite3_vtab_config()` 的虚拟表（其
xCreate 或 xConnect 方法调用的）不支持约束。在此配置（默认）下，若对 xUpdate 方法的
调用返回 `SQLITE_CONSTRAINT`，则整个语句被回滚，仿佛用户的 SQL 语句中指定了 OR ABORT，
无论实际指定了什么 ON CONFLICT 模式。

若 X 非零，则虚拟表实现保证：若 xUpdate 返回 `SQLITE_CONSTRAINT`，它会在对内部或持久
数据结构做任何修改之前返回。若 ON CONFLICT 模式是 ABORT、FAIL、IGNORE 或 ROLLBACK，
SQLite 能够回滚语句或数据库事务，并根据情况放弃或继续处理当前 SQL 语句。若 ON CONFLICT
模式是 REPLACE 且 xUpdate 方法返回 `SQLITE_CONSTRAINT`，SQLite 会把它当作 ON CONFLICT
模式为 ABORT 来处理。

需要处理 OR REPLACE 的虚拟表实现必须在 xUpdate 方法内处理。若对 `sqlite3_vtab_on_conflict()`
函数的调用指示当前 ON CONFLICT 策略是 REPLACE，虚拟表实现应在 xUpdate 回调内静默替换
适当的行并返回 `SQLITE_OK`。或者，若无法做到，可以返回 `SQLITE_CONSTRAINT`，此时
SQLite 回退到 OR ABORT 约束处理。

**SQLITE_VTAB_DIRECTONLY**：虚拟表实现的 xConnect 或 xCreate 方法内部的
`sqlite3_vtab_config(db,SQLITE_VTAB_DIRECTONLY)` 形式的调用，禁止该虚拟表被用于触发器
和视图中。

**SQLITE_VTAB_INNOCUOUS**：虚拟表实现的 xConnect 或 xCreate 方法内部的
`sqlite3_vtab_config(db,SQLITE_VTAB_INNOCUOUS)` 形式的调用，将该虚拟表标识为可在
触发器和视图中安全使用。从概念上说，`SQLITE_VTAB_INNOCUOUS` 标记表示：即使虚拟表被
恶意黑客控制，也不会造成严重伤害。开发者应避免设置 `SQLITE_VTAB_INNOCUOUS` 标志，
除非绝对必要。

**SQLITE_VTAB_USES_ALL_SCHEMAS**：虚拟表实现的 xConnect 或 xCreate 方法内部的
`sqlite3_vtab_config(db,SQLITE_VTAB_USES_ALL_SCHEMAS)` 形式的调用，指示查询规划器：
每当使用该虚拟表时，至少在所有的 schema（"main"、"temp" 以及任何 ATTACH 的数据库）上
开始一个读事务。

## Win32 目录类型（Win32 Directory Types）

```
#define SQLITE_WIN32_DATA_DIRECTORY_TYPE  1
#define SQLITE_WIN32_TEMP_DIRECTORY_TYPE  2
```

这些宏只在 Windows 上可用。它们定义 sqlite3_win32_set_directory 接口的 type 参数的
允许值。

## 运行时限制类别（Run-Time Limit Categories）

```
#define SQLITE_LIMIT_LENGTH                    0
#define SQLITE_LIMIT_SQL_LENGTH                1
#define SQLITE_LIMIT_COLUMN                    2
#define SQLITE_LIMIT_EXPR_DEPTH                3
#define SQLITE_LIMIT_COMPOUND_SELECT           4
#define SQLITE_LIMIT_VDBE_OP                   5
#define SQLITE_LIMIT_FUNCTION_ARG              6
#define SQLITE_LIMIT_ATTACHED                  7
#define SQLITE_LIMIT_LIKE_PATTERN_LENGTH       8
#define SQLITE_LIMIT_VARIABLE_NUMBER           9
#define SQLITE_LIMIT_TRIGGER_DEPTH            10
#define SQLITE_LIMIT_WORKER_THREADS           11
#define SQLITE_LIMIT_PARSER_DEPTH             12
```

这些常量定义各种性能限制，可用 `sqlite3_limit()` 在运行时降低。下面给出这些限制的简洁
描述，更多信息见 Limits in SQLite。

**SQLITE_LIMIT_LENGTH**：任何字符串、BLOB 或表行的最大大小，以字节计。

**SQLITE_LIMIT_SQL_LENGTH**：SQL 语句的最大长度，以字节计。

**SQLITE_LIMIT_COLUMN**：表定义、SELECT 结果集中的最大列数，或索引、ORDER BY、
GROUP BY 子句中的最大列数。

**SQLITE_LIMIT_EXPR_DEPTH**：任何表达式解析树的最大深度，以及子查询和视图的最大嵌套
深度。

**SQLITE_LIMIT_PARSER_DEPTH**：用于分析输入 SQL 语句的 LALR(1) 解析器栈的最大深度。

**SQLITE_LIMIT_COMPOUND_SELECT**：复合 SELECT 语句中的最大项数。

**SQLITE_LIMIT_VDBE_OP**：用于实现 SQL 语句的虚拟机程序中的最大指令数。若
`sqlite3_prepare_v2()` 或等价物尝试在单个预编译语句中为超过这么多操作码分配空间，则
返回 `SQLITE_NOMEM` 错误。

**SQLITE_LIMIT_FUNCTION_ARG**：函数上的最大参数个数。

**SQLITE_LIMIT_ATTACHED**：附加数据库的最大数量。

**SQLITE_LIMIT_LIKE_PATTERN_LENGTH**：LIKE 或 GLOB 算子的模式参数的最大长度。

**SQLITE_LIMIT_VARIABLE_NUMBER**：SQL 语句中任何参数的最大索引号。

**SQLITE_LIMIT_TRIGGER_DEPTH**：触发器递归的最大深度，以及独立触发器的最大嵌套深度。

**SQLITE_LIMIT_WORKER_THREADS**：单个预编译语句可以启动的辅助工作线程的最大数量。

## 数据库连接的状态参数（Status Parameters for database connections）

```
#define SQLITE_DBSTATUS_LOOKASIDE_USED       0
#define SQLITE_DBSTATUS_CACHE_USED           1
#define SQLITE_DBSTATUS_SCHEMA_USED          2
#define SQLITE_DBSTATUS_STMT_USED            3
#define SQLITE_DBSTATUS_LOOKASIDE_HIT        4
#define SQLITE_DBSTATUS_LOOKASIDE_MISS_SIZE  5
#define SQLITE_DBSTATUS_LOOKASIDE_MISS_FULL  6
#define SQLITE_DBSTATUS_CACHE_HIT            7
#define SQLITE_DBSTATUS_CACHE_MISS           8
#define SQLITE_DBSTATUS_CACHE_WRITE          9
#define SQLITE_DBSTATUS_DEFERRED_FKS        10
#define SQLITE_DBSTATUS_CACHE_USED_SHARED   11
#define SQLITE_DBSTATUS_CACHE_SPILL         12
#define SQLITE_DBSTATUS_TEMPBUF_SPILL       13
#define SQLITE_DBSTATUS_MAX                 13   /* 最大的已定义 DBSTATUS */
```

这些常量是可作为 `sqlite3_db_status()` 接口第二参数传入的可用整数 "verbs"。

未来版本的 SQLite 可能加入新的 verbs。现有 verbs 可能停用。应用应检查 `sqlite3_db_status()`
的返回码以确保调用生效。若调用已停用或不支持的 verb，`sqlite3_db_status()` 接口将返回
非零错误码。

**SQLITE_DBSTATUS_LOOKASIDE_USED**：此参数返回当前借出的 lookaside 内存槽数。

**SQLITE_DBSTATUS_LOOKASIDE_HIT**：此参数返回用 lookaside 内存满足的 malloc 尝试数。
只有高水位值有意义；当前值总是零。

**SQLITE_DBSTATUS_LOOKASIDE_MISS_SIZE**：此参数返回本可用 lookaside 内存满足、但因
请求的内存量大而失败（大于 lookaside 槽大小）的 malloc 尝试数。只有高水位值有意义；
当前值总是零。

**SQLITE_DBSTATUS_LOOKASIDE_MISS_FULL**：此参数返回本可用 lookaside 内存满足、但因
所有 lookaside 内存已在使用而失败的 malloc 尝试数。只有高水位值有意义；当前值总是零。

**SQLITE_DBSTATUS_CACHE_USED**：此参数返回与数据库连接关联的所有 pager 缓存使用的堆
内存字节数的近似值。与 `SQLITE_DBSTATUS_CACHE_USED` 关联的高水位标记总是 0。

**SQLITE_DBSTATUS_CACHE_USED_SHARED**：此参数与 DBSTATUS_CACHE_USED 类似，区别是若
pager 缓存在两个或多个连接之间共享，则该 pager 缓存使用的堆内存字节数在附加连接之间
均分。换句话说，若与数据库连接关联的 pager 缓存都没有共享，则此请求返回与
DBSTATUS_CACHE_USED 相同的值。若一个或多个 pager 缓存共享，则此调用返回的值将小于
DBSTATUS_CACHE_USED 返回的值。与 `SQLITE_DBSTATUS_CACHE_USED_SHARED` 关联的高水位
标记总是 0。

**SQLITE_DBSTATUS_SCHEMA_USED**：此参数返回用于存储与连接关联的所有数据库（main、
temp 以及任何 ATTACH 的数据库）的 schema 的堆内存字节数的近似值。即使因启用共享缓存
模式而使 schema 内存与其它数据库连接共享，也会报告 schema 使用的全部内存量。与
`SQLITE_DBSTATUS_SCHEMA_USED` 关联的高水位标记总是 0。

**SQLITE_DBSTATUS_STMT_USED**：此参数返回与数据库连接关联的所有预编译语句使用的堆和
lookaside 内存字节数的近似值。与 `SQLITE_DBSTATUS_STMT_USED` 关联的高水位标记总是 0。

**SQLITE_DBSTATUS_CACHE_HIT**：此参数返回已发生的 pager 缓存命中次数。与
`SQLITE_DBSTATUS_CACHE_HIT` 关联的高水位标记总是 0。

**SQLITE_DBSTATUS_CACHE_MISS**：此参数返回已发生的 pager 缓存未命中次数。与
`SQLITE_DBSTATUS_CACHE_MISS` 关联的高水位标记总是 0。

**SQLITE_DBSTATUS_CACHE_WRITE**：此参数返回已写入磁盘的脏缓存条目数。具体地说，是
wal 模式数据库中写入 wal 文件的页数、或回滚模式数据库中写入数据库文件的页数。作为
事务回滚或数据库恢复操作的一部分写入的任何页不包含在内。若写页到磁盘时发生 I/O 或
其它错误，对后续 `SQLITE_DBSTATUS_CACHE_WRITE` 请求的影响未定义。与
`SQLITE_DBSTATUS_CACHE_WRITE` 关联的高水位标记总是 0。

此参数（`SQLITE_DBSTATUS_CACHE_WRITE`）测量的量与 `SQLITE_DBSTATUS_TEMPBUF_SPILL`
有重叠。重置其中一个会减少另一个。

**SQLITE_DBSTATUS_CACHE_SPILL**：此参数返回因页缓存溢出、在事务中途写入磁盘的脏缓存
条目数。事务一次性全部写入磁盘时更高效。页在事务中途溢出会引入额外开销。此参数可用于
帮助识别可通过增大缓存大小解决的效率低下。

**SQLITE_DBSTATUS_DEFERRED_FKS**：当且仅当所有外键约束（延迟或立即）都已解析时，此
参数的当前值为零。高水位标记总是 0。

**SQLITE_DBSTATUS_TEMPBUF_SPILL**：此参数返回写入磁盘临时文件的字节数，这些字节若有
足够内存本可保存在内存中。此值包括：作为复杂查询一部分的中间表写入、溢出到磁盘的外部
排序、以及对 TEMP 表的写入。高水位标记总是 0。

此参数（`SQLITE_DBSTATUS_TEMPBUF_SPILL`）测量的量与 `SQLITE_DBSTATUS_CACHE_WRITE`
有重叠。重置其中一个会减少另一个。

## 预编译语句的状态参数（Status Parameters for prepared statements）

```
#define SQLITE_STMTSTATUS_FULLSCAN_STEP     1
#define SQLITE_STMTSTATUS_SORT              2
#define SQLITE_STMTSTATUS_AUTOINDEX         3
#define SQLITE_STMTSTATUS_VM_STEP           4
#define SQLITE_STMTSTATUS_REPREPARE         5
#define SQLITE_STMTSTATUS_RUN               6
#define SQLITE_STMTSTATUS_FILTER_MISS       7
#define SQLITE_STMTSTATUS_FILTER_HIT        8
#define SQLITE_STMTSTATUS_MEMUSED           99
```

这些预处理宏定义命名与 `sqlite3_stmt_status()` 接口关联的计数器值的整数代码。各计数器
的含义如下：

**SQLITE_STMTSTATUS_FULLSCAN_STEP**：这是 SQLite 作为全表扫描一部分在表中向前步进的
次数。此计数器数值大可能表明有机会通过谨慎使用索引来提高性能。

**SQLITE_STMTSTATUS_SORT**：这是已发生的排序操作次数。此计数器非零值可能表明有机会
通过谨慎使用索引来提高性能。

**SQLITE_STMTSTATUS_AUTOINDEX**：这是插入到自动创建的瞬态索引中的行数，这些索引是为
帮助连接运行更快而自动创建的。此计数器非零值可能表明有机会通过添加无需每次运行语句都
重新初始化的永久索引来提高性能。

**SQLITE_STMTSTATUS_VM_STEP**：若该数小于或等于 2147483647，这是预编译语句执行的
虚拟机操作数。虚拟机操作数可用作预编译语句完成总工作量的代理。若虚拟机操作数超过
2147483647，则此语句状态码返回的值未定义。

**SQLITE_STMTSTATUS_REPREPARE**：这是预编译语句因 schema 更改、或可能影响查询计划的
绑定参数更改而自动重新生成的次数。

**SQLITE_STMTSTATUS_RUN**：这是预编译语句已被运行的次数。就此计数器而言，一次"运行"
是一次或多次 `sqlite3_step()` 调用后跟一次 `sqlite3_reset()` 调用。

计数器在每次循环的第一个
`sqlite3_step()` 调用时递增。

**SQLITE_STMTSTATUS_FILTER_HIT** 和 **SQLITE_STMTSTATUS_FILTER_MISS**：
`SQLITE_STMTSTATUS_FILTER_HIT` 是 Bloom 过滤器返回未找到、从而跳过连接步骤的次数。
对应的 `SQLITE_STMTSTATUS_FILTER_MISS` 值是 Bloom 过滤器返回找到、从而连接步骤不得不
正常处理的次数。

**SQLITE_STMTSTATUS_MEMUSED**：这是用于存储预编译语句的堆内存字节数的近似值。此值
实际上不是计数器，因此操作码为 `SQLITE_STMTSTATUS_MEMUSED` 时，`sqlite3_stmt_status()`
的 resetFlg 参数被忽略。

## 数据库快照（Database Snapshot）

```
typedef struct sqlite3_snapshot {
  unsigned char hidden[48];
} sqlite3_snapshot;
```

快照对象的一个实例记录 WAL 模式数据库在某个特定历史时间点的状态。

在 WAL 模式下，打开在同一数据库文件上的多个数据库连接可以各自读取数据库文件的不同
历史版本。当数据库连接开始读事务时，该连接看到数据库在事务开始时间点存在的不可变副本。
其它连接对数据库的后续更改，在新读事务开始前不会被读者看到。

sqlite3_snapshot 对象记录数据库文件历史版本的状态信息，以便以后可能打开一个新的读
事务，看到该历史版本的数据库，而不是最近的版本。

- 1 个使用此对象的构造函数：sqlite3_snapshot_get()
- 1 个使用此对象的析构函数：sqlite3_snapshot_free()
- 3 个使用此对象的方法：
  - sqlite3_snapshot_cmp()
  - sqlite3_snapshot_open()
  - sqlite3_snapshot_recover()

## 64 位整数类型（64-Bit Integer Types）

```
#ifdef SQLITE_INT64_TYPE
  typedef SQLITE_INT64_TYPE sqlite_int64;
# ifdef SQLITE_UINT64_TYPE
    typedef SQLITE_UINT64_TYPE sqlite_uint64;
# else
    typedef unsigned SQLITE_INT64_TYPE sqlite_uint64;
# endif
#elif defined(_MSC_VER) || defined(__BORLANDC__)
  typedef __int64 sqlite_int64;
  typedef unsigned __int64 sqlite_uint64;
#else
  typedef long long int sqlite_int64;
  typedef unsigned long long int sqlite_uint64;
#endif
typedef sqlite_int64 sqlite3_int64;
typedef sqlite_uint64 sqlite3_uint64;
```

因为没有跨平台方式指定 64 位整数类型，SQLite 包含 64 位有符号和无符号整数的 typedef。

sqlite3_int64 和 sqlite3_uint64 是首选类型定义。sqlite_int64 和 sqlite_uint64 类型仅为
向后兼容而支持。

sqlite3_int64 和 sqlite_int64 类型可以存储 -9223372036854775808 到 +9223372036854775807
（含）之间的整数值。sqlite3_uint64 和 sqlite_uint64 类型可以存储 0 到
+18446744073709551615（含）之间的整数值。

## 虚拟表对象（Virtual Table Object）

```
struct sqlite3_module {
  int iVersion;
  int (*xCreate)(sqlite3*, void *pAux,
               int argc, const char *const*argv,
               sqlite3_vtab **ppVTab, char**);
  int (*xConnect)(sqlite3*, void *pAux,
               int argc, const char *const*argv,
               sqlite3_vtab **ppVTab, char**);
  int (*xBestIndex)(sqlite3_vtab *pVTab, sqlite3_index_info*);
  int (*xDisconnect)(sqlite3_vtab *pVTab);
  int (*xDestroy)(sqlite3_vtab *pVTab);
  int (*xOpen)(sqlite3_vtab *pVTab, sqlite3_vtab_cursor **ppCursor);
  int (*xClose)(sqlite3_vtab_cursor*);
  int (*xFilter)(sqlite3_vtab_cursor*, int idxNum, const char *idxStr,
                int argc, sqlite3_value **argv);
  int (*xNext)(sqlite3_vtab_cursor*);
  int (*xEof)(sqlite3_vtab_cursor*);
  int (*xColumn)(sqlite3_vtab_cursor*, sqlite3_context*, int);
  int (*xRowid)(sqlite3_vtab_cursor*, sqlite3_int64 *pRowid);
  int (*xUpdate)(sqlite3_vtab *, int, sqlite3_value **, sqlite3_int64 *);
  int (*xBegin)(sqlite3_vtab *pVTab);
  int (*xSync)(sqlite3_vtab *pVTab);
  int (*xCommit)(sqlite3_vtab *pVTab);
  int (*xRollback)(sqlite3_vtab *pVTab);
  int (*xFindFunction)(sqlite3_vtab *pVtab, int nArg, const char *zName,
                       void (**pxFunc)(sqlite3_context*,int,sqlite3_value**),
                       void **ppArg);
  int (*xRename)(sqlite3_vtab *pVtab, const char *zNew);
  /* 以上方法在 sqlite_module 对象的第 1 版中。下面的是第 2 版及更高版本。 */
  int (*xSavepoint)(sqlite3_vtab *pVTab, int);
  int (*xRelease)(sqlite3_vtab *pVTab, int);
  int (*xRollbackTo)(sqlite3_vtab *pVTab, int);
  /* 以上方法在 sqlite_module 对象的第 1 版和第 2 版中。下面的是第 3 版及更高版本。 */
  int (*xShadowName)(const char*);
  /* 以上方法在 sqlite_module 对象的第 1 版到第 3 版中。下面的是第 4 版及更高版本。 */
  int (*xIntegrity)(sqlite3_vtab *pVTab, const char *zSchema,
                    const char *zTabName, int mFlags, char **pzErr);
};
```

此结构（有时称为"虚拟表模块"）定义虚拟表的实现。此结构主要由模块的方法组成。

通过填充此结构的持久实例、并把指向该实例的指针传给 `sqlite3_create_module()` 或
`sqlite3_create_module_v2()` 来创建虚拟表模块。注册在它被不同模块替换、或数据库连接
关闭之前一直有效。此结构的内容在它被任何数据库连接注册期间不得改变。

## 虚拟表游标对象（Virtual Table Cursor Object）

```
struct sqlite3_vtab_cursor {
  sqlite3_vtab *pVtab;      /* 本游标的虚拟表 */
  /* 虚拟表实现通常会追加额外字段 */
};
```

每个虚拟表模块实现都使用以下结构的子类来描述指向虚拟表、并用于遍历虚拟表的游标。
游标用模块的 xOpen 方法创建，用 xClose 方法销毁。游标被模块的 xFilter、xNext、xEof、
xColumn 和 xRowid 方法使用。每个模块实现都会定义游标结构的内容以适合其自身需要。

此超类存在的目的是定义所有实现共有的游标字段。

## 打开的 BLOB 的句柄（A Handle To An Open BLOB）

```
typedef struct sqlite3_blob sqlite3_blob;
```

此对象的实例表示可以在其上执行增量 BLOB I/O 的打开 BLOB。此类型的对象由
`sqlite3_blob_open()` 创建、由 `sqlite3_blob_close()` 销毁。`sqlite3_blob_read()` 和
`sqlite3_blob_write()` 接口可用于读写 BLOB 的小段。`sqlite3_blob_bytes()` 接口返回
BLOB 的字节大小。

- 1 个使用此对象的构造函数：sqlite3_blob_open()
- 1 个使用此对象的析构函数：sqlite3_blob_close()
- 4 个使用此对象的方法：
  - sqlite3_blob_bytes()
  - sqlite3_blob_read()
  - sqlite3_blob_reopen()
  - sqlite3_blob_write()

## 数据库连接句柄（Database Connection Handle）

```
typedef struct sqlite3 sqlite3;
```

每个打开的 SQLite 数据库由指向名为 "sqlite3" 的不透明结构实例的指针表示。把 sqlite3
指针看作一个对象很有用。`sqlite3_open()`、`sqlite3_open16()` 和 `sqlite3_open_v2()`
接口是它的构造函数，`sqlite3_close()` 和 `sqlite3_close_v2()` 是它的析构函数。还有
许多其它接口（例如 `sqlite3_prepare_v2()`、`sqlite3_create_function()` 和
`sqlite3_busy_timeout()`，仅举三例）是 sqlite3 对象上的方法。

- 3 个使用此对象的构造函数：
  - sqlite3_open()
  - sqlite3_open16()
  - sqlite3_open_v2()
- 2 个使用此对象的析构函数：
  - sqlite3_close()
  - sqlite3_close_v2()
- 80 个使用此对象的方法：

sqlite3_autovacuum_pages

- sqlite3_blob_open
- sqlite3_busy_handler
- sqlite3_busy_timeout
- sqlite3_changes
- sqlite3_changes64
- sqlite3_collation_needed
- sqlite3_collation_needed16
- sqlite3_commit_hook
- sqlite3_create_collation
- sqlite3_create_collation16
- sqlite3_create_collation_v2
- sqlite3_create_function
- sqlite3_create_function16
- sqlite3_create_function_v2
- sqlite3_create_module
- sqlite3_create_module_v2
- sqlite3_create_window_function
- sqlite3_db_cacheflush
- sqlite3_db_config
- sqlite3_db_filename
- sqlite3_db_mutex
- sqlite3_db_name
- sqlite3_db_readonly
- sqlite3_db_release_memory
- sqlite3_db_status
- sqlite3_db_status64
- sqlite3_drop_modules
- sqlite3_enable_load_extension
- sqlite3_errcode
- sqlite3_errmsg
- sqlite3_errmsg16
- sqlite3_error_offset
- sqlite3_errstr
- sqlite3_exec
- sqlite3_extended_errcode
- sqlite3_extended_result_codes
- sqlite3_file_control
- sqlite3_free_table
- sqlite3_get_autocommit
- sqlite3_get_clientdata


- sqlite3_get_table
- sqlite3_interrupt
- sqlite3_is_interrupted
- sqlite3_last_insert_rowid
- sqlite3_limit
- sqlite3_load_extension
- sqlite3_next_stmt
- sqlite3_overload_function
- sqlite3_prepare
- sqlite3_prepare16
- sqlite3_prepare16_v2
- sqlite3_prepare16_v3
- sqlite3_prepare_v2
- sqlite3_prepare_v3
- sqlite3_preupdate_blobwrite
- sqlite3_preupdate_count
- sqlite3_preupdate_depth
- sqlite3_preupdate_hook
- sqlite3_preupdate_new
- sqlite3_preupdate_old
- sqlite3_progress_handler
- sqlite3_rollback_hook
- sqlite3_set_authorizer
- sqlite3_set_clientdata
- sqlite3_set_errmsg
- sqlite3_set_last_insert_rowid
- sqlite3_setlk_timeout
- sqlite3_system_errno
- sqlite3_table_column_metadata
- sqlite3_total_changes
- sqlite3_total_changes64
- sqlite3_trace_v2
- sqlite3_txn_state
- sqlite3_unlock_notify
- sqlite3_update_hook
- sqlite3_wal_autocheckpoint
- sqlite3_wal_checkpoint
- sqlite3_wal_checkpoint_v2
- sqlite3_wal_hook

## 动态字符串对象（Dynamic String Object）

```
typedef struct sqlite3_str sqlite3_str;
```

sqlite3_str 对象的一个实例包含正在构造的动态大小字符串。

sqlite3_str 对象的生命周期如下：

- 用 `sqlite3_str_new()` 创建 sqlite3_str 对象。
- 用各种方法（如 `sqlite3_str_appendf()`）向 sqlite3_str 对象追加文本。
- 用 `sqlite3_str_finish()` 接口销毁 sqlite3_str 对象并返回它创建的字符串。

- 1 个使用此对象的构造函数：sqlite3_str_new()
- 2 个使用此对象的析构函数：sqlite3_str_finish()、sqlite3_str_free()
- 10 个使用此对象的方法：

sqlite3_str_append

- sqlite3_str_appendall
- sqlite3_str_appendchar
- sqlite3_str_appendf
- sqlite3_str_errcode
- sqlite3_str_length
- sqlite3_str_reset
- sqlite3_str_truncate
- sqlite3_str_value
- sqlite3_str_vappendf

## 应用自定义页缓存（Application Defined Page Cache）

```
typedef struct sqlite3_pcache_methods2 sqlite3_pcache_methods2;
struct sqlite3_pcache_methods2 {
  int iVersion;
  void *pArg;
  int (*xInit)(void*);
  void (*xShutdown)(void*);
  sqlite3_pcache *(*xCreate)(int szPage, int szExtra, int bPurgeable);
  void (*xCachesize)(sqlite3_pcache*, int nCachesize);
  int (*xPagecount)(sqlite3_pcache*);
  sqlite3_pcache_page *(*xFetch)(sqlite3_pcache*, unsigned key, int createFlag);
  void (*xUnpin)(sqlite3_pcache*, sqlite3_pcache_page*, int discard);
  void (*xRekey)(sqlite3_pcache*, sqlite3_pcache_page*,
      unsigned oldKey, unsigned newKey);
  void (*xTruncate)(sqlite3_pcache*, unsigned iLimit);
  void (*xDestroy)(sqlite3_pcache*);
  void (*xShrink)(sqlite3_pcache*);
};
```

`sqlite3_config(SQLITE_CONFIG_PCACHE2, ...)` 接口可以通过传入 sqlite3_pcache_methods2
结构的实例来注册替代页缓存实现。在许多应用中，SQLite 分配的大部分堆内存用于页缓存。
通过用此 API 实现自定义页缓存，应用可以更好地控制 SQLite 消耗的内存量、内存分配和释放
的方式、以及用于确定数据库文件哪些部分被缓存、缓存多久的策略。

替代页缓存机制是极端措施，只有最苛刻的应用才需要。对大多数用途推荐内建页缓存。

sqlite3_pcache_methods2 结构的内容由 SQLite 在 sqlite3_config 调用内复制到内部缓冲区。
因此应用可以在 `sqlite3_config()` 调用返回后丢弃该参数。

xInit() 方法对每次有效的 `sqlite3_initialize()` 调用调用一次（通常在进程生命周期内只
调用一次）。xInit() 方法收到 sqlite3_pcache_methods2.pArg 值的副本。xInit() 方法的意图
是建立自定义页缓存实现所需的全局数据结构。若 xInit() 方法为 NULL，则用内建默认页缓存
代替应用自定义页缓存。

xShutdown() 方法由 `sqlite3_shutdown()` 调用。可在进程关闭前清理任何未释放的资源
（如需要）。xShutdown() 方法可以为 NULL。

SQLite 自动串行化对 xInit 方法的调用，因此 xInit 方法无需是线程安全的。xShutdown 方法
只从 `sqlite3_shutdown()` 调用，因此也无需是线程安全的。在多线程应用中，所有其它方法
必须是线程安全的。

SQLite 绝不会在没有间隔的 xShutdown() 调用的情况下多次调用 xInit()。

SQLite 调用 xCreate() 方法构造新的缓存实例。SQLite 通常为每个打开的数据库文件创建一个
缓存实例，但这不保证。第一个参数 szPage 是缓存必须分配的页的字节大小。szPage 将总是 2
的幂。第二个参数 szExtra 是与每个页缓存条目关联的额外存储字节数。szExtra 参数将是一个
小于 250 的数。SQLite 将用每页额外的 szExtra 字节存储关于磁盘上底层数据库页的元数据。
传入 szExtra 的值取决于 SQLite 版本、目标平台以及 SQLite 的编译方式。xCreate() 的第三
个参数 bPurgeable 为真，表示创建的缓存将用于缓存存储在磁盘上的文件的数据库页；为假，
表示用于内存数据库。缓存实现不必基于 bPurgeable 的值做任何特别处理；它纯粹是建议性的。
在 bPurgeable 为假的缓存上，SQLite 绝不会调用 xUnpin()，除非故意删除页。换句话说，对
bPurgeable 设为假的缓存调用 xUnpin()，其 "discard" 标志将总是设为真。因此，bPurgeable
设为假的缓存绝不会包含任何未固定（unpinned）的页。

SQLite 可在任何时刻调用 xCachesize() 方法，为第一个参数传入的缓存实例设置建议的最大
缓存大小（存储的页数）。这是用 SQLite "PRAGMA cache_size" 命令配置的值。与 bPurgeable
参数一样，实现不必对此值做任何事；它只是建议性的。

xPagecount() 方法必须返回当前存储在缓存中的页数，包括固定（pinned）和未固定的页。

xFetch() 方法在缓存中定位一页，返回与该页关联的 sqlite3_pcache_page 对象的指针，或
NULL 指针。返回的 sqlite3_pcache_page 对象的 pBuf 元素将是指向 szPage 字节缓冲区的
指针，用于存储单个数据库页的内容。sqlite3_pcache_page 的 pExtra 元素将是指向 SQLite
为页缓存中每个条目请求的 szExtra 字节额外存储的指针。

要获取的页由 key 确定。最小 key 值为 1。用 xFetch 取回后，该页被视为"固定"。

若请求的页已在页缓存中，则页缓存实现必须返回内容完整的页缓冲区指针。若请求的页还不在
缓存中，则缓存实现应使用 createFlag 参数的值帮助决定采取什么动作：

SQLite 通常以 createFlag 0 或 1 调用 xFetch()。SQLite 只会在先前以 createFlag 1 的调用
失败后使用 createFlag 2。在 xFetch() 调用之间，SQLite 可能尝试通过把固定页的内容溢出到
磁盘、并同步操作系统磁盘缓存来取消固定一个或多个缓存页。

xUnpin() 由 SQLite 以当前固定页的指针作为第二个参数调用。若第三个参数 discard 非零，
则必须从缓存中逐出该页。若 discard 参数为零，则页缓存实现可自行决定丢弃或保留该页。页
缓存实现可随时选择逐出未固定的页。

缓存不得进行任何引用计数。无论先前调用过多少次 xFetch()，单次 xUnpin() 调用都会取消
固定该页。

xRekey() 方法用于改变作为第二个参数传入的页关联的 key 值。若缓存先前包含与 newKey
关联的条目，必须丢弃它。任何先前与 newKey 关联的缓存条目保证未被固定。

当 SQLite 调用 xTruncate() 方法时，缓存必须丢弃所有页号（key）大于或等于传给 xTruncate()
的 iLimit 参数值的现有缓存条目。若其中任何页被固定，它们被隐式地取消固定，意味着可以
安全地丢弃。

xDestroy() 方法用于删除 xCreate() 分配的缓存。应释放与指定缓存关联的所有资源。调用
xDestroy() 方法后，SQLite 认为 sqlite3_pcache* 句柄无效，不会把它与任何其它
sqlite3_pcache_methods2 函数一起使用。

当 SQLite 希望页缓存释放尽可能多的堆内存时，调用 xShrink() 方法。页缓存实现没有义务
释放任何内存，但行为良好的实现应尽力而为。

## 预编译语句对象（Prepared Statement Object）

```
typedef struct sqlite3_stmt sqlite3_stmt;
```

此对象的实例表示一条已被编译成二进制形式、准备好被求值的单个 SQL 语句。

把每条 SQL 语句看作一个独立的计算机程序。原始 SQL 文本是源代码。预编译语句对象是编译
后的目标代码。所有 SQL 在被运行前都必须转换成预编译语句。

预编译语句对象的生命周期通常如下：

- 用 `sqlite3_prepare_v2()` 创建预编译语句对象。
- 用 `sqlite3_bind_*()` 接口把值绑定到参数。
- 通过调用 `sqlite3_step()` 一次或多次运行 SQL。
- 用 `sqlite3_reset()` 重置预编译语句，然后回到步骤 2。做零次或多次。
- 用 `sqlite3_finalize()` 销毁对象。

- 6 个使用此对象的构造函数：

sqlite3_prepare

- sqlite3_prepare16
- sqlite3_prepare16_v2
- sqlite3_prepare16_v3
- sqlite3_prepare_v2
- sqlite3_prepare_v3

- 1 个使用此对象的析构函数：sqlite3_finalize()
- 53 个使用此对象的方法：

- sqlite3_bind_blob
- sqlite3_bind_blob64
- sqlite3_bind_double
- sqlite3_bind_int
- sqlite3_bind_int64
- sqlite3_bind_null
- sqlite3_bind_parameter_count
- sqlite3_bind_parameter_index
- sqlite3_bind_parameter_name
- sqlite3_bind_pointer
- sqlite3_bind_text
- sqlite3_bind_text16
- sqlite3_bind_text64
- sqlite3_bind_value
- sqlite3_bind_zeroblob
- sqlite3_bind_zeroblob64
- sqlite3_clear_bindings
- sqlite3_column_blob
- sqlite3_column_bytes
- sqlite3_column_bytes16
- sqlite3_column_count
- sqlite3_column_database_name
- sqlite3_column_database_name16
- sqlite3_column_decltype
- sqlite3_column_decltype16
- sqlite3_column_double
- sqlite3_column_int
- sqlite3_column_int64
- sqlite3_column_name
- sqlite3_column_name16
- sqlite3_column_origin_name
- sqlite3_column_origin_name16
- sqlite3_column_table_name
- sqlite3_column_table_name16
- sqlite3_column_text
- sqlite3_column_text16
- sqlite3_column_type
- sqlite3_column_value
- sqlite3_data_count
- sqlite3_db_handle
- sqlite3_expanded_sql
- sqlite3_normalized_sql
- sqlite3_reset
- sqlite3_sql
- sqlite3_step
- sqlite3_stmt_busy
- sqlite3_stmt_explain
- sqlite3_stmt_isexplain
- sqlite3_stmt_readonly
- sqlite3_stmt_scanstatus
- sqlite3_stmt_scanstatus_reset
- sqlite3_stmt_scanstatus_v2
- sqlite3_stmt_status

## 动态类型值对象（Dynamically Typed Value Object）

```
typedef struct sqlite3_value sqlite3_value;
```

SQLite 用 sqlite3_value 对象表示可以存储在数据库表中的所有值。SQLite 对存储的值使用
动态类型。存储在 sqlite3_value 对象中的值可以是整数、浮点值、字符串、BLOB 或 NULL。

sqlite3_value 对象可以是"受保护的"或"不受保护的"。某些接口要求受保护的 sqlite3_value。
其它接口接受受保护或不受保护的 sqlite3_value。每个接受 sqlite3_value 参数的接口都指定
它是否要求受保护的 sqlite3_value。`sqlite3_value_dup()` 接口可用于从不受保护的
sqlite3_value 构造新的受保护的 sqlite3_value。

术语"受保护的"和"不受保护的"指是否持有互斥锁。受保护的 sqlite3_value 对象持有内部
互斥锁，不受保护的 sqlite3_value 对象不持有互斥锁。若 SQLite 被编译为单线程
（`SQLITE_THREADSAFE=0` 且 `sqlite3_threadsafe()` 返回 0）、或 SQLite 运行在减少互斥
锁模式之一（`SQLITE_CONFIG_SINGLETHREAD` 或 `SQLITE_CONFIG_MULTITHREAD`）下，则受保护
和不受保护的 sqlite3_value 对象之间没有区别、可以互换使用。但为了最大代码可移植性，
建议应用即使不是严格需要，也仍然区分受保护和不受保护的 sqlite3_value 对象。

作为参数传入应用自定义 SQL 函数实现的 sqlite3_value 对象是受保护的。
`sqlite3_vtab_rhs_value()` 返回的 sqlite3_value 对象是受保护的。`sqlite3_column_value()`
返回的 sqlite3_value 对象是不受保护的。不受保护的 sqlite3_value 对象只能用作
`sqlite3_result_value()`、`sqlite3_bind_value()` 和 `sqlite3_value_dup()` 的参数。
`sqlite3_value_type()` 系列接口要求受保护的 sqlite3_value 对象。

- 19 个使用此对象的方法：

- sqlite3_value_blob
- sqlite3_value_bytes
- sqlite3_value_bytes16
- sqlite3_value_double
- sqlite3_value_dup
- sqlite3_value_encoding
- sqlite3_value_free
- sqlite3_value_frombind
- sqlite3_value_int
- sqlite3_value_int64
- sqlite3_value_nochange
- sqlite3_value_numeric_type
- sqlite3_value_pointer
- sqlite3_value_subtype
- sqlite3_value_text
- sqlite3_value_text16
- sqlite3_value_text16be
- sqlite3_value_text16le
- sqlite3_value_type

## 废弃函数（Deprecated Functions）

```
#ifndef SQLITE_OMIT_DEPRECATED
int sqlite3_aggregate_count(sqlite3_context*);
int sqlite3_expired(sqlite3_stmt*);
int sqlite3_transfer_bindings(sqlite3_stmt*, sqlite3_stmt*);
int sqlite3_global_recover(void);
void sqlite3_thread_cleanup(void);
int sqlite3_memory_alarm(void(*)(void*,sqlite3_int64,int),
                      void*,sqlite3_int64);
#endif
```

这些函数已废弃。为保持与旧代码的向后兼容，这些函数继续被支持。但新应用应避免使用这些
函数。为鼓励程序员避免这些函数，我们将不解释它们是做什么的。

## 在线备份 API（Online Backup API）

```
sqlite3_backup *sqlite3_backup_init(
  sqlite3 *pDest,                        /* 目标数据库句柄 */
  const char *zDestName,                 /* 目标数据库名 */
  sqlite3 *pSource,                      /* 源数据库句柄 */
  const char *zSourceName                /* 源数据库名 */
);
int sqlite3_backup_step(sqlite3_backup *p, int nPage);
int sqlite3_backup_finish(sqlite3_backup *p);
int sqlite3_backup_remaining(sqlite3_backup *p);
int sqlite3_backup_pagecount(sqlite3_backup *p);
```

备份 API 把一个数据库的内容复制到另一个数据库。它既可用于创建数据库备份，也可用于把
内存数据库复制到持久文件、或从持久文件复制到内存数据库。

另见：使用 SQLite 在线备份 API

SQLite 在备份操作期间对目标数据库文件持有打开的写事务。源数据库只在被读取时加读锁；
不会在整个备份操作期间持续加锁。因此，可以对正在使用的源数据库执行备份，而不会阻止其它
数据库连接在备份进行期间读取或写入源数据库。

执行备份操作：

- 调用 `sqlite3_backup_init()` 一次初始化备份，
- 调用 `sqlite3_backup_step()` 一次或多次在两个数据库之间传输数据，最后
- 调用 `sqlite3_backup_finish()` 释放与备份操作关联的所有资源。

每次成功调用 `sqlite3_backup_init()` 都应恰好有一次对 `sqlite3_backup_finish()` 的调用。

**sqlite3_backup_init()**

`sqlite3_backup_init(D,N,S,M)` 的 D 和 N 参数分别是与目标数据库关联的数据库连接和
数据库名。数据库名对主数据库是 "main"、对临时数据库是 "temp"、对附加数据库是 ATTACH
语句中 AS 关键字之后指定的名字。传给 `sqlite3_backup_init(D,N,S,M)` 的 S 和 M 参数分别
标识源数据库的数据库连接和数据库名。源和目标数据库连接（参数 S 和 D）必须不同，否则
`sqlite3_backup_init(D,N,S,M)` 将以错误失败。


若目标数据库上已打开读事务或读写事务，对 `sqlite3_backup_init()` 的调用将失败、返回
NULL。

若 `sqlite3_backup_init(D,N,S,M)` 内发生错误，则返回 NULL，错误码和错误消息存储在目标
数据库连接 D 中。可用 `sqlite3_errcode()`、`sqlite3_errmsg()` 和/或 `sqlite3_errmsg16()`
函数取回对 `sqlite3_backup_init()` 失败调用的错误码和消息。成功的 `sqlite3_backup_init()`
调用返回指向 sqlite3_backup 对象的指针。sqlite3_backup 对象可与 `sqlite3_backup_step()`
和 `sqlite3_backup_finish()` 函数一起使用来执行指定的备份操作。

**sqlite3_backup_step()**

函数 `sqlite3_backup_step(B,N)` 将在 sqlite3_backup 对象 B 指定的源和目标数据库之间复制
最多 N 页。若 N 为负，则复制所有剩余的源页。若 `sqlite3_backup_step(B,N)` 成功复制 N 页、
且仍有更多页要复制，则函数返回 `SQLITE_OK`。若 `sqlite3_backup_step(B,N)` 成功完成把所有
页从源复制到目标，则返回 `SQLITE_DONE`。若运行 `sqlite3_backup_step(B,N)` 时发生错误，
则返回错误码。除 `SQLITE_OK` 和 `SQLITE_DONE` 外，对 `sqlite3_backup_step()` 的调用可能
返回 `SQLITE_READONLY`、`SQLITE_NOMEM`、`SQLITE_BUSY`、`SQLITE_LOCKED` 或某个
`SQLITE_IOERR_XXX` 扩展错误码。

`sqlite3_backup_step()` 在以下情况可能返回 `SQLITE_READONLY`：

- 目标数据库以只读方式打开，或
- 目标数据库使用预写日志日志记录、且目标和源页大小不同，或
- 目标数据库是内存数据库、且目标和源页大小不同。

若 `sqlite3_backup_step()` 无法取得所需的文件系统锁，则调用忙处理器函数（若指定了）。
若锁可用之前忙处理器返回非零，则向调用者返回 `SQLITE_BUSY`。此时可稍后重试对
`sqlite3_backup_step()` 的调用。若调用 `sqlite3_backup_step()` 时源数据库连接正被用于
写入源数据库，则立即返回 `SQLITE_LOCKED`。同样，此时可稍后重试该调用。若返回
`SQLITE_IOERR_XXX`、`SQLITE_NOMEM` 或 `SQLITE_READONLY`，则重试对 `sqlite3_backup_step()`
的调用没有意义。这些错误被视为致命错误。应用必须接受备份操作已失败，并把备份操作句柄传给
`sqlite3_backup_finish()` 以释放相关资源。

第一次调用 `sqlite3_backup_step()` 获得目标文件的排他锁。排他锁直到调用
`sqlite3_backup_finish()`、或备份操作完成且 `sqlite3_backup_step()` 返回 `SQLITE_DONE`
时才释放。每次调用 `sqlite3_backup_step()` 都获得源数据库的共享锁，该锁持续到
`sqlite3_backup_step()` 调用结束。由于源数据库在 `sqlite3_backup_step()` 调用之间不被
加锁，源数据库可能在备份过程进行到一半时被修改。若源数据库被外部进程、或通过备份操作所用
之外的数据库连接修改，则备份将被下一次 `sqlite3_backup_step()` 调用自动重新启动。若源
数据库被备份操作所用的同一个数据库连接修改，则备份数据库同时被自动更新。

**sqlite3_backup_finish()**

当 `sqlite3_backup_step()` 已返回 `SQLITE_DONE`、或应用希望放弃备份操作时，应用应把它
传给 `sqlite3_backup_finish()` 来销毁 sqlite3_backup。`sqlite3_backup_finish()` 接口释放
与 sqlite3_backup 对象关联的所有资源。若 `sqlite3_backup_step()` 尚未返回 `SQLITE_DONE`，
则目标数据库上任何活动的写事务被回滚。sqlite3_backup 对象在调用 `sqlite3_backup_finish()`
后无效、不得再使用。

若没有发生 `sqlite3_backup_step()` 错误，`sqlite3_backup_finish` 返回 `SQLITE_OK`，无论
`sqlite3_backup_step()` 是否完成。若在同一个 sqlite3_backup 对象上任何先前的
`sqlite3_backup_step()` 调用期间发生内存不足或 I/O 错误，则 `sqlite3_backup_finish()` 返回
相应的错误码。

从 `sqlite3_backup_step()` 返回 `SQLITE_BUSY` 或 `SQLITE_LOCKED` 不是永久错误，不影响
`sqlite3_backup_finish()` 的返回值。

**sqlite3_backup_remaining() 和 sqlite3_backup_pagecount()**

`sqlite3_backup_remaining()` 例程返回最近一次 `sqlite3_backup_step()` 结束时仍要备份的
页数。`sqlite3_backup_pagecount()` 例程返回最近一次 `sqlite3_backup_step()` 结束时源
数据库的总页数。这些函数返回的值只由 `sqlite3_backup_step()` 更新。若源数据库以改变源
数据库大小或剩余页数的方式被修改，那些更改在下一次 `sqlite3_backup_step()` 之前不会反映
在 `sqlite3_backup_pagecount()` 和 `sqlite3_backup_remaining()` 的输出中。

**数据库句柄的并发使用**

备份操作进行中或正在初始化时，应用可以把源数据库连接用于其它用途。若 SQLite 被编译和
配置为支持线程安全的数据库连接，则源数据库连接可从其它线程并发使用。

但应用必须保证：在调用 `sqlite3_backup_init()` 之后、到对应调用 `sqlite3_backup_finish()`
之前，不把目标数据库连接传给任何其它 API（由任何线程）。SQLite 当前不检查应用是否错误
访问目标数据库连接，因此不报告错误码，但操作仍可能出故障。备份进行期间使用目标数据库连接
也可能导致互斥锁死锁。

若在共享缓存模式下运行，应用必须保证备份运行期间不访问目标数据库使用的共享缓存。实际上，
这意味着应用必须保证正在备份到的磁盘文件不被进程内任何连接访问，而不只是传给
`sqlite3_backup_init()` 的那个特定连接。

sqlite3_backup 对象本身是部分线程安全的。多个线程可以安全地对 `sqlite3_backup_step()`
做多次并发调用。但 `sqlite3_backup_remaining()` 和 `sqlite3_backup_pagecount()` API
严格来说不是线程安全的。若在另一线程正在调用 `sqlite3_backup_step()` 的同时调用它们，
它们可能返回无效值。

**使用备份 API 的替代方案**

安全创建 SQLite 数据库一致性备份的其它技术包括：

- VACUUM INTO 命令。
- sqlite3_rsync 实用程序。

## 把数组值绑定到 CARRAY 表值函数（Bind array values to the CARRAY table-valued function）

```
int sqlite3_carray_bind_v2(
  sqlite3_stmt *pStmt,        /* 要绑定的语句 */
  int i,                      /* 参数索引 */
  void *aData,                /* 指向数组数据的指针 */
  int nData,                  /* 数据元素个数 */
  int mFlags,                 /* CARRAY 标志 */
  void (*xDel)(void*),        /* aData 的析构函数 */
  void *pDel                  /* 传给 xDel() 的可选参数 */
);
int sqlite3_carray_bind(
  sqlite3_stmt *pStmt,        /* 要绑定的语句 */
  int i,                      /* 参数索引 */
  void *aData,                /* 指向数组数据的指针 */
  int nData,                  /* 数据元素个数 */
  int mFlags,                 /* CARRAY 标志 */
  void (*xDel)(void*)         /* aData 的析构函数 */
);
```

`sqlite3_carray_bind_v2(S,I,P,N,F,X,D)` 接口把数组值绑定到作为 carray() 表值函数第一个
参数的参数上。S 参数是指向使用 carray() 函数的预编译语句的指针。I 是要绑定的参数索引。
I 必须是作为 carray() 表值函数第一个参数的参数的索引。P 是指向要绑定的数组的指针，N 是
数组中的元素个数。F 参数是 `SQLITE_CARRAY_INT32`、`SQLITE_CARRAY_INT64`、
`SQLITE_CARRAY_DOUBLE`、`SQLITE_CARRAY_TEXT` 或 `SQLITE_CARRAY_BLOB` 常量之一，指示数组
P 的数据类型。

若 X 参数不是 NULL 指针、也不是 `SQLITE_STATIC` 或 `SQLITE_TRANSIENT` 特殊值之一，则
SQLite 在用完 P 中的数据时调用函数 X、以 D 为参数。调用 X(D) 是数组 P 的析构函数。即使
对 `sqlite3_carray_bind_v2()` 的调用失败，也会调用析构函数 X(D)。若 X 参数是特殊值
`SQLITE_STATIC`，则 SQLite 假定数据是静态的、从不调用析构函数。若 X 参数是特殊值
`SQLITE_TRANSIENT`，则 `sqlite3_carray_bind_v2()` 在返回前制作数据的私有副本、从不调用
析构函数 X。

`sqlite3_carray_bind()` 函数与 `sqlite3_carray_bind_v2()` 工作方式相同，只是 D 参数设为
P。换句话说，`sqlite3_carray_bind(S,I,P,N,F,X)` 与
`sqlite3_carray_bind_v2(S,I,P,N,F,X,P)` 相同。

## 计算被修改的行数（Count The Number Of Rows Modified）

```
int sqlite3_changes(sqlite3*);
sqlite3_int64 sqlite3_changes64(sqlite3*);
```

这些函数返回指定参数上的数据库连接中、最近一次完成的 INSERT、UPDATE 或 DELETE 语句修改、
插入或删除的行数。除返回值的类型不同外，这两个函数相同；且若最近一次 INSERT、UPDATE 或
DELETE 修改的行数大于 "int" 类型支持的最大值，则 `sqlite3_changes()` 的返回值未定义。
执行任何其它类型的 SQL 语句不会修改这些函数返回的值。就此接口而言，CREATE TABLE AS SELECT
语句不算 INSERT、UPDATE 或 DELETE 语句，因此 CREATE TABLE AS SELECT 语句添加到新表的行
不被计数。

只考虑 INSERT、UPDATE 或 DELETE 语句直接做的更改——由触发器、外键动作或 REPLACE 约束
解决引起的辅助更改不被计数。

被 INSTEAD OF 触发器拦截的视图更改不被计数。在视图上运行 INSERT、UPDATE 或 DELETE 语句
后立即调用 `sqlite3_changes()` 返回的值总是零。只计算对真实表做的更改。

若在触发器程序运行期间执行 `sqlite3_changes()` 函数，事情更复杂。这可能发生：若程序使用
changes() SQL 函数，或某个其它回调函数直接调用 `sqlite3_changes()`。基本地：

- 进入触发器程序前，`sqlite3_changes()` 函数返回的值被保存。触发器程序结束后，恢复原值。
- 在触发器程序内，每条 INSERT、UPDATE 和 DELETE 语句照常在其完成时设置
  `sqlite3_changes()` 返回的值。当然，此值不包含子触发器执行的任何更改，因为
  `sqlite3_changes()` 值会在每个子触发器运行后被保存和恢复。

这意味着：若触发器内第一条 INSERT、UPDATE 或 DELETE 语句使用 changes() SQL 函数（或类似
函数），它返回调用语句开始执行时设置的值。若触发器程序内第二条或后续此类语句使用它，返回
的值反映同一触发器内先前 INSERT、UPDATE 或 DELETE 语句修改的行数。

若在 `sqlite3_changes()` 运行期间另一线程对同一数据库连接做更改，则返回的值不可预测、
无意义。

另见：

- `sqlite3_total_changes()` 接口
- count_changes pragma
- changes() SQL 函数
- data_version pragma

## 关闭数据库连接（Closing A Database Connection）

```
int sqlite3_close(sqlite3*);
int sqlite3_close_v2(sqlite3*);
```

`sqlite3_close()` 和 `sqlite3_close_v2()` 例程是 sqlite3 对象的析构函数。若 sqlite3 对象
被成功销毁、且所有相关资源被释放，对 `sqlite3_close()` 和 `sqlite3_close_v2()` 的调用
返回 `SQLITE_OK`。

理想情况下，应用应在尝试关闭对象之前终结所有预编译语句、关闭所有 BLOB 句柄、完成所有与
sqlite3 对象关联的 sqlite3_backup 对象。若数据库连接与未终结的预编译语句、未关闭的 BLOB
句柄和/或未完成的 sqlite3_backup 对象关联，则 `sqlite3_close()` 会让数据库连接保持打开并
返回 `SQLITE_BUSY`。若 `sqlite3_close_v2()` 在存在未终结预编译语句、未关闭 BLOB 句柄和/或
未完成 sqlite3_backup 时被调用，它无论如何返回 `SQLITE_OK`，但不是立即释放数据库连接，
而是把数据库连接标记为不可用的 "zombie"（僵尸），并安排在所有预编译语句被终结、所有 BLOB
句柄被关闭、所有备份完成后自动释放数据库连接。`sqlite3_close_v2()` 接口供使用垃圾回收、
且析构函数调用顺序任意的宿主语言使用。


若 sqlite3 对象在事务打开时被销毁，事务被自动回滚。

`sqlite3_close(C)` 和 `sqlite3_close_v2(C)` 的 C 参数必须是 NULL 指针、或从
`sqlite3_open()`、`sqlite3_open16()` 或 `sqlite3_open_v2()` 获得、且先前未关闭的 sqlite3
对象指针。用 NULL 指针参数调用 `sqlite3_close()` 或 `sqlite3_close_v2()` 是无害的空操作。

## 排序规则请求回调（Collation Needed Callbacks）

```
int sqlite3_collation_needed(
  sqlite3*,
  void*,
  void(*)(void*,sqlite3*,int eTextRep,const char*)
);
int sqlite3_collation_needed16(
  sqlite3*,
  void*,
  void(*)(void*,sqlite3*,int eTextRep,const void*)
);
```

为避免在数据库能使用前必须注册所有排序规则，可向数据库连接注册单个回调函数，每当需要
未定义的排序规则时调用它。

若用 `sqlite3_collation_needed()` API 注册函数，则未定义排序规则的名字以 UTF-8 编码的
字符串传入。若用 `sqlite3_collation_needed16()`，则名字以机器本机字节序的 UTF-16 传入。
对任一函数的调用都会替换现有的 collation-needed 回调。

调用回调时，第一个传入参数是 `sqlite3_collation_needed()` 或 `sqlite3_collation_needed16()`
第二个参数的副本。第二个参数是数据库连接。第三个参数是 `SQLITE_UTF8`、`SQLITE_UTF16BE`
或 `SQLITE_UTF16LE` 之一，指示所需排序规则函数最理想的形式。第四个参数是所需排序规则的
名字。

回调函数应使用 `sqlite3_create_collation()`、`sqlite3_create_collation16()` 或
`sqlite3_create_collation_v2()` 注册所需的排序规则。

## 查询结果中数据的来源（Source Of Data In A Query Result）

```
const char *sqlite3_column_database_name(sqlite3_stmt*,int);
const void *sqlite3_column_database_name16(sqlite3_stmt*,int);
const char *sqlite3_column_table_name(sqlite3_stmt*,int);
const void *sqlite3_column_table_name16(sqlite3_stmt*,int);
const char *sqlite3_column_origin_name(sqlite3_stmt*,int);
const void *sqlite3_column_origin_name16(sqlite3_stmt*,int);
```

这些例程提供一种方法，确定 SELECT 语句中特定结果列的来源数据库、表和表列。数据库、表或
列的名字可以作为 UTF-8 或 UTF-16 字符串返回。_database_ 例程返回数据库名，_table_ 例程
返回表名，origin_ 例程返回列名。返回的字符串在预编译语句用 `sqlite3_finalize()` 销毁之前、
或在语句被某次运行的第一次 `sqlite3_step()` 调用自动重新准备之前、或在再次以不同编码请求
相同信息之前有效。

返回的名字是数据库、表和列的原始未别名名字。

这些接口的第一个参数是预编译语句。这些函数返回关于语句返回的第 N 个结果列的信息，其中
N 是函数的第二个参数。对这些例程，最左边的列是列 0。

若语句返回的第 N 列是表达式或子查询、不是列值，则所有这些函数返回 NULL。若发生内存分配
错误，这些例程也可能返回 NULL。否则，它们返回查询结果列从中提取的附加数据库、表或列的
名字。

与所有其它 SQLite API 一样，名字以 "16" 结尾的返回 UTF-16 编码字符串，其它函数返回
UTF-8。

这些 API 仅在库用 `SQLITE_ENABLE_COLUMN_METADATA` C 预处理符号编译时可用。

若两个或多个线程同时为同一个预编译语句和结果列调用一个或多个列元数据接口，则结果未定义。

## 查询结果的声明数据类型（Declared Datatype Of A Query Result）

```
const char *sqlite3_column_decltype(sqlite3_stmt*,int);
const void *sqlite3_column_decltype16(sqlite3_stmt*,int);
```

第一个参数是预编译语句。若此语句是 SELECT 语句、且该 SELECT 返回结果集的第 N 列是表列
（不是表达式或子查询），则返回该表列的声明类型。若结果集的第 N 列是表达式或子查询，则
返回 NULL 指针。返回的字符串总是 UTF-8 编码。

例如，给定数据库 schema：

```
CREATE TABLE t1(c1 VARIANT);
```

以及要编译的以下语句：

```
SELECT c1 + 1, c1 FROM t1;
```

此例程将为第二个结果列（i==1）返回字符串 "VARIANT"，为第一个结果列（i==0）返回 NULL
指针。

SQLite 使用动态运行时类型。因此仅仅因为某列声明为包含特定类型，并不意味着存储在该列中的
数据是该声明类型。SQLite 是强类型的，但类型是动态的而非静态的。类型与单个值关联，而非与
容纳这些值的容器关联。

## 结果集中的列名（Column Names In A Result Set）

```
const char *sqlite3_column_name(sqlite3_stmt*, int N);
const void *sqlite3_column_name16(sqlite3_stmt*, int N);
```

这些例程返回分配给 SELECT 语句结果集中特定列的名字。`sqlite3_column_name()` 接口返回
指向零结尾 UTF-8 字符串的指针，`sqlite3_column_name16()` 返回指向零结尾 UTF-16 字符串的
指针。第一个参数是实现该 SELECT 语句的预编译语句。第二个参数是列号。最左边的列是 0。

返回的字符串指针在预编译语句被 `sqlite3_finalize()` 销毁之前、或语句被某次运行的第一次
`sqlite3_step()` 调用自动重新准备之前、或下一次对同一列调用 `sqlite3_column_name()` 或
`sqlite3_column_name16()` 之前有效。

若 `sqlite3_malloc()` 在处理任一例程期间失败（例如在 UTF-8 到 UTF-16 转换期间），则返回
NULL 指针。

结果列的名字是该列的 "AS" 子句的值（若有 AS 子句）。若无 AS 子句，则列名未指定、可能随
SQLite 版本而变。

## 提交和回滚通知回调（Commit And Rollback Notification Callbacks）

```
void *sqlite3_commit_hook(sqlite3*, int(*)(void*), void*);
void *sqlite3_rollback_hook(sqlite3*, void(*)(void *), void*);
```

`sqlite3_commit_hook()` 接口注册回调函数，每当事务被提交时调用。先前对同一数据库连接调用
`sqlite3_commit_hook()` 设置的任何回调都被覆盖。`sqlite3_rollback_hook()` 接口注册回调
函数，每当事务被回滚时调用。先前对同一数据库连接调用 `sqlite3_rollback_hook()` 设置的任何
回调都被覆盖。pArg 参数被传递到回调。若提交钩子函数的回调返回非零，则提交被转换为回滚。

`sqlite3_commit_hook(D,C,P)` 和 `sqlite3_rollback_hook(D,C,P)` 函数返回同一数据库连接 D
上先前调用同一函数的 P 参数；若是对 D 上每个函数的第一次调用，则返回 NULL。

提交和回滚钩子回调不可重入。回调实现不得做任何会修改调用它的数据库连接的事情。任何修改
数据库连接的动作必须推迟到最初触发提交或回滚钩子的 `sqlite3_step()` 调用完成之后。注意：
运行任何其它 SQL 语句（包括 SELECT 语句）、或仅仅调用 `sqlite3_prepare_v2()` 和
`sqlite3_step()`，都会就本段中的"修改"而言修改数据库连接。

注册 NULL 函数会禁用回调。

当提交钩子回调例程返回零时，允许 COMMIT 操作正常继续。若提交钩子返回非零，则 COMMIT 被
转换为 ROLLBACK。因提交钩子返回非零而导致的回滚会调用回滚钩子，就像任何其它回滚一样。

就此 API 而言，若执行显式 "ROLLBACK" 语句、或错误或约束导致隐式回滚发生，则称事务已被
回滚。若事务因数据库连接被关闭而自动回滚，则不调用回滚回调。

另见 `sqlite3_update_hook()` 接口。

## 运行时库编译期选项诊断（Run-Time Library Compilation Options Diagnostics）

```
#ifndef SQLITE_OMIT_COMPILEOPTION_DIAGS
int sqlite3_compileoption_used(const char *zOptName);
const char *sqlite3_compileoption_get(int N);
#else
# define sqlite3_compileoption_used(X) 0
# define sqlite3_compileoption_get(X)  ((void*)0)
#endif
```

`sqlite3_compileoption_used()` 函数返回 0 或 1，指示指定选项是否在编译期被定义。传给
`sqlite3_compileoption_used()` 的选项名可省略 `SQLITE_` 前缀。

`sqlite3_compileoption_get()` 函数通过返回第 N 个编译期选项字符串，允许遍历编译期定义的
选项列表。若 N 越界，`sqlite3_compileoption_get()` 返回 NULL 指针。`sqlite3_compileoption_get()`
返回的任何字符串都省略 `SQLITE_` 前缀。

可通过在编译期指定 `SQLITE_OMIT_COMPILEOPTION_DIAGS` 选项省略对诊断函数
`sqlite3_compileoption_used()` 和 `sqlite3_compileoption_get()` 的支持。

另见：SQL 函数 sqlite_compileoption_used() 和 sqlite_compileoption_get() 以及
compile_options pragma。

## 判断 SQL 语句是否完整（Determine If An SQL Statement Is Complete）

```
int sqlite3_complete(const char *sql);
int sqlite3_complete16(const void *sql);
```

这些例程在命令行输入期间很有用，用于判断当前输入的文本是否看起来构成完整的 SQL 语句、
还是需要更多输入才能把文本送入 SQLite 解析。若输入字符串看起来是完整的 SQL 语句，这些
例程返回 1。若语句以分号记号结尾、且不是格式良好的 CREATE TRIGGER 语句的前缀，则判定语句
完整。字符串字面量或引号标识符名或注释内嵌的分号不是独立记号（它们是嵌入的记号的一部分），
因此不算语句终止符。最后一个分号之后的空白和注释被忽略。

若语句不完整，这些例程返回 0。若内存分配失败，则返回 `SQLITE_NOMEM`。

这些例程不解析 SQL 语句，因此不会检测语法不正确的 SQL。

若在调用 `sqlite3_complete16()` 之前 SQLite 未用 `sqlite3_initialize()` 初始化，则
`sqlite3_complete16()` 自动调用 `sqlite3_initialize()`。若该初始化失败，则无论输入 SQL
是否完整，`sqlite3_complete16()` 的返回值都将非零。

`sqlite3_complete()` 的输入必须是零结尾 UTF-8 字符串。

`sqlite3_complete16()` 的输入必须是本机字节序的零结尾 UTF-16 字符串。

## 定义新的排序规则（Define New Collating Sequences）

```

int sqlite3_create_collation(
  sqlite3*,

  const char *zName,
  int eTextRep,
  void *pArg,
  int(*xCompare)(void*,int,const void*,int,const void*)
);
int sqlite3_create_collation_v2(
  sqlite3*,
  const char *zName,
  int eTextRep,
  void *pArg,
  int(*xCompare)(void*,int,const void*,int,const void*),
  void(*xDestroy)(void*)
);
int sqlite3_create_collation16(
  sqlite3*,
  const void *zName,
  int eTextRep,
  void *pArg,
  int(*xCompare)(void*,int,const void*,int,const void*)
);
```

这些函数添加、移除或修改与第一个参数指定的数据库连接关联的排序规则。

`sqlite3_create_collation()` 和 `sqlite3_create_collation_v2()` 的排序规则名是 UTF-8
字符串，`sqlite3_create_collation16()` 的是本机字节序的 UTF-16 字符串。根据
`sqlite3_strnicmp()` 比较相等的排序规则名被视为同一个名字。

第三个参数（eTextRep）必须是以下常量之一：

- `SQLITE_UTF8`
- `SQLITE_UTF16LE`
- `SQLITE_UTF16BE`
- `SQLITE_UTF16`
- `SQLITE_UTF16_ALIGNED`

eTextRep 参数决定传给排序函数回调 xCompare 的字符串编码。eTextRep 的 `SQLITE_UTF16`
和 `SQLITE_UTF16_ALIGNED` 值强制字符串为本机字节序的 UTF-16。eTextRep 的
`SQLITE_UTF16_ALIGNED` 值强制字符串从偶数字节地址开始。

第四个参数 pArg 是应用数据指针，作为第一个参数传给排序函数回调。

第五个参数 xCompare 是指向排序函数的指针。可以用相同的名字、不同的 eTextRep 参数注册
多个排序函数，SQLite 将使用需要最少数据转换的那个。若 xCompare 参数为 NULL，则删除该
排序函数。当所有具有相同名字的排序函数都被删除时，该排序规则不再可用。

调用排序函数回调时传入 pArg 应用数据指针的副本、以及 eTextRep 参数指定编码的两个字符串。
排序函数回调的两个整数参数是两个字符串的长度（以字节计）。排序函数必须返回整数：若第一个
字符串小于、等于或大于第二个字符串，分别返回负值、零或正值。排序函数对相同输入必须总是
返回相同答案。若两个或多个排序函数注册到同一个排序规则名（使用不同 eTextRep 值），则所有
函数在收到等价字符串时必须给出等价答案。排序函数必须对所有字符串 A、B 和 C 遵守以下性质：

- 若 A==B 则 B==A。
- 若 A==B 且 B==C 则 A==C。
- 若 A（以下两行源文本在此处因抓取损坏而残缺，无法翻译）

`sqlite3_prepare_v2()` 接口与带零 prepFlags 参数的 `sqlite3_prepare_v3()` 工作方式
完全相同。

## 编译期授权回调（Compile-Time Authorization Callbacks）

```
int sqlite3_set_authorizer(
  sqlite3*,
  int (*xAuth)(void*,int,const char*,const char*,const char*,const char*),
  void *pUserData
);
```

此例程向第一个参数提供的特定数据库连接注册授权器回调。当 SQL 语句被 `sqlite3_prepare()`
或其变体 `sqlite3_prepare_v2()`、`sqlite3_prepare_v3()`、`sqlite3_prepare16()`、
`sqlite3_prepare16_v2()` 和 `sqlite3_prepare16_v3()` 编译时，调用授权器回调。在编译过程
的各个点、为执行各种动作而创建逻辑时，调用授权器回调检查这些动作是否被允许。授权器回调
应返回 `SQLITE_OK` 允许动作、`SQLITE_IGNORE` 不允许该特定动作但允许 SQL 语句继续编译、
或 `SQLITE_DENY` 使整个 SQL 语句以错误被拒绝。若授权器回调返回 `SQLITE_IGNORE`、
`SQLITE_OK` 或 `SQLITE_DENY` 之外的任何值，则触发授权器的 `sqlite3_prepare_v2()` 或等价
调用将带错误消息失败。

当回调返回 `SQLITE_OK`，表示所请求的操作没问题。当回调返回 `SQLITE_DENY`，则触发授权器
的 `sqlite3_prepare_v2()` 或等价调用将带解释访问被拒绝的错误消息失败。

授权器回调的第一个参数是 `sqlite3_set_authorizer()` 接口第三个参数的副本。回调的第二个
参数是指定要授权的特定动作的整数动作码。回调的第三个到第六个参数是 NULL 指针或包含关于
要授权动作的额外细节的零结尾字符串。应用必须总是准备好遇到授权回调第三到第六个参数中的
任何一个为 NULL 指针。

若动作码是 `SQLITE_READ` 且回调返回 `SQLITE_IGNORE`，则构造的预编译语句将用 NULL 值替代
本会读取（若返回 `SQLITE_OK`）的表列。`SQLITE_IGNORE` 返回可用于拒绝不可信用户访问表的
个别列。当 SELECT 引用表、但不从该表提取列值（例如在 "SELECT count(*) FROM tab" 这类查询
中）时，对该表调用一次 `SQLITE_READ` 授权器回调，列名为空字符串。若动作码是 `SQLITE_DELETE`
且回调返回 `SQLITE_IGNORE`，则 DELETE 操作继续，但禁用 truncate 优化、逐行删除所有行。

授权器在从不信任来源准备 SQL 语句时使用，以确保 SQL 语句不试图访问它们不被允许看到的数据、
或不试图执行损坏数据库的恶意语句。例如，应用可能允许用户输入任意 SQL 查询供数据库求值。
但应用不希望用户能对数据库做任意更改。则可在准备用户输入的 SQL 时设置授权器，禁止除
SELECT 语句外的一切。

需要处理来自不可信来源的 SQL 的应用，除了使用授权器外，还可能考虑用 `sqlite3_limit()` 降低
资源限制、用 max_page_count PRAGMA 限制数据库大小。

数据库连接上一次只能有一个授权器。每次调用 `sqlite3_set_authorizer` 都覆盖先前调用。通过
安装 NULL 回调禁用授权器。授权器默认禁用。

授权器回调不得做任何会修改调用它的数据库连接的事情。注意：就本段中的"修改"而言，
`sqlite3_prepare_v2()` 和 `sqlite3_step()` 都会修改它们的数据库连接。

用 `sqlite3_prepare_v2()` 准备语句时，语句可能在 `sqlite3_step()` 期间因 schema 更改而被
重新准备。因此应用应确保在 `sqlite3_step()` 期间正确的授权器回调保持就位。

注意：授权器回调只在 `sqlite3_prepare()` 或其变体期间调用。语句求值期间的 `sqlite3_step()`
不执行授权，除非如上一段所述，`sqlite3_step()` 在 schema 更改后调用 `sqlite3_prepare_v2()`
重新准备语句。

## 测试自动提交模式（Test For Auto-Commit Mode）

```
int sqlite3_get_autocommit(sqlite3*);
```

`sqlite3_get_autocommit()` 接口在给定数据库连接处于自动提交模式时返回非零、否则返回零。
自动提交模式默认开启。BEGIN 语句禁用自动提交模式。COMMIT 或 ROLLBACK 重新启用自动提交
模式。

若多语句事务中的语句发生某些种类的错误（包括 `SQLITE_FULL`、`SQLITE_IOERR`、
`SQLITE_NOMEM`、`SQLITE_BUSY` 和 `SQLITE_INTERRUPT`），事务可能被自动回滚。找出 SQLite
是否在错误后自动回滚事务的唯一方法是使用此函数。

若此例程运行时另一线程改变数据库连接的自动提交状态，则返回值未定义。

## 注册处理 SQLITE_BUSY 错误的回调（Register A Callback To Handle SQLITE_BUSY Errors）

```
int sqlite3_busy_handler(sqlite3*,int(*)(void*,int),void*);
```

`sqlite3_busy_handler(D,X,P)` 例程设置回调函数 X，当另一线程或进程锁定了表、而尝试访问
与数据库连接 D 关联的数据库表时，可能以参数 P 调用它。`sqlite3_busy_handler()` 接口用于
实现 `sqlite3_busy_timeout()` 和 PRAGMA busy_timeout。

若忙回调为 NULL，则遇到锁时立即返回 `SQLITE_BUSY`。若忙回调非 NULL，则可能以两个参数调用
回调。

忙处理器的第一个参数是 `sqlite3_busy_handler()` 第三个参数 void\* 指针的副本。忙处理器回调
的第二个参数是同一锁定事件先前调用忙处理器的次数。若忙回调返回 0，则不再尝试访问数据库、
向应用返回 `SQLITE_BUSY`。若回调返回非零，则再次尝试访问数据库、循环重复。

忙处理器的存在不保证在锁争用时它会被调用。若 SQLite 确定调用忙处理器可能导致死锁，它将
直接向应用返回 `SQLITE_BUSY` 而非调用忙处理器。考虑这样一个场景：一个进程持有正试图提升为
保留锁的读锁，第二个进程持有正试图提升为排他锁的保留锁。第一个进程无法继续，因为它被第二个
阻塞；第二个进程无法继续，因为它被第一个阻塞。若两个进程都调用忙处理器，则都不会取得进展。
因此 SQLite 为第一个进程返回 `SQLITE_BUSY`，希望这会促使第一个进程释放其读锁、让第二个进程
继续。

默认忙回调为 NULL。

每个数据库连接只能定义一个忙处理器。设置新的忙处理器会清除任何先前设置的处理器。注意：
调用 `sqlite3_busy_timeout()` 或求值 PRAGMA busy_timeout=N 会改变忙处理器、从而清除任何
先前设置的忙处理器。

忙回调不应采取任何会修改调用它的数据库连接的动作。换句话说，忙处理器不可重入。任何此类
动作都导致未定义行为。

忙处理器不得关闭调用它的数据库连接或预编译语句。

## 查询的结果值（Result Values From A Query）

```
const void *sqlite3_column_blob(sqlite3_stmt*, int iCol);
double sqlite3_column_double(sqlite3_stmt*, int iCol);
int sqlite3_column_int(sqlite3_stmt*, int iCol);
sqlite3_int64 sqlite3_column_int64(sqlite3_stmt*, int iCol);
const unsigned char *sqlite3_column_text(sqlite3_stmt*, int iCol);
const void *sqlite3_column_text16(sqlite3_stmt*, int iCol);
sqlite3_value *sqlite3_column_value(sqlite3_stmt*, int iCol);
int sqlite3_column_bytes(sqlite3_stmt*, int iCol);
int sqlite3_column_bytes16(sqlite3_stmt*, int iCol);
int sqlite3_column_type(sqlite3_stmt*, int iCol);
```

摘要：

细节：

这些例程返回关于查询当前结果行单个列的信息。在所有情况下，第一个参数是指向正在求值的预编译
语句的指针（从 `sqlite3_prepare_v2()` 或其变体返回的 sqlite3_stmt\*），第二个参数是应返回
信息的列索引。结果集最左边的列索引为 0。结果中的列数可用 `sqlite3_column_count()` 确定。

若 SQL 语句当前不指向有效行、或列索引越界，则结果未定义。这些例程只能在最近一次对
`sqlite3_step()` 的调用返回 `SQLITE_ROW`、且其后既未调用 `sqlite3_reset()` 也未调用
`sqlite3_finalize()` 时调用。若在 `sqlite3_reset()` 或 `sqlite3_finalize()` 之后、或在
`sqlite3_step()` 返回 `SQLITE_ROW` 之外的值之后调用其中任何例程，则结果未定义。

若在这些例程中的任何一个待处理时、另一线程调用 `sqlite3_step()`、`sqlite3_reset()` 或
`sqlite3_finalize()`，则结果未定义。

前六个接口（_blob、_double、_int、_int64、_text 和 _text16）各以特定数据格式返回结果列的
值。若结果列最初不是所请求的格式（例如查询返回整数、但用 `sqlite3_column_text()` 接口提取
值），则执行自动类型转换。

`sqlite3_column_type()` 例程返回结果列初始数据类型的类型码。返回的值是 `SQLITE_INTEGER`、
`SQLITE_FLOAT`、`SQLITE_TEXT`、`SQLITE_BLOB` 或 `SQLITE_NULL` 之一。可用
`sqlite3_column_type()` 的返回值决定应使用前六个接口中的哪一个提取列值。只有对相关值未发生
自动类型转换时，`sqlite3_column_type()` 返回的值才有意义。类型转换后，调用
`sqlite3_column_type()` 的结果未定义（尽管无害）。未来版本的 SQLite 可能改变类型转换后
`sqlite3_column_type()` 的行为。

若结果是 BLOB 或 TEXT 字符串，则可用 `sqlite3_column_bytes()` 或 `sqlite3_column_bytes16()`
接口确定该 BLOB 或字符串的大小。

若结果是 BLOB 或 UTF-8 字符串，则 `sqlite3_column_bytes()` 例程返回该 BLOB 或字符串中的
字节数。若结果是 UTF-16 字符串，则 `sqlite3_column_bytes()` 把字符串转换为 UTF-8、然后返回
字节数。若结果是数值，则 `sqlite3_column_bytes()` 用 `sqlite3_snprintf()` 把该值转换为
UTF-8 字符串、并返回该字符串的字节数。若结果是 NULL，则 `sqlite3_column_bytes()` 返回零。

若结果是 BLOB 或 UTF-16 字符串，则 `sqlite3_column_bytes16()` 例程返回该 BLOB 或字符串中的
字节数。若结果是 UTF-8 字符串，则 `sqlite3_column_bytes16()` 把字符串转换为 UTF-16、然后
返回字节数。若结果是数值，则 `sqlite3_column_bytes16()` 用 `sqlite3_snprintf()` 把该值转换
为 UTF-16 字符串、并返回该字符串的字节数。若结果是 NULL，则 `sqlite3_column_bytes16()` 返回
零。

`sqlite3_column_bytes()` 和 `sqlite3_column_bytes16()` 返回的值不包括字符串末尾的零结尾符。
为清楚起见：`sqlite3_column_bytes()` 和 `sqlite3_column_bytes16()` 返回的值是字符串中的字节
数，而非字符数。

`sqlite3_column_text()` 和 `sqlite3_column_text16()` 返回的字符串（即使是空字符串）总是零
结尾。零长度 BLOB 的 `sqlite3_column_blob()` 返回值为 NULL 指针。

`sqlite3_column_text16()` 返回的字符串总是平台本机字节序，无论为数据库设置的文本编码如何。

> 警告：`sqlite3_column_value()` 返回的对象是不受保护的 sqlite3_value 对象。在多线程环境中，
> 不受保护的 sqlite3_value 对象只能与 `sqlite3_bind_value()` 和 `sqlite3_result_value()` 安全
> 使用。若以任何其它方式使用 `sqlite3_column_value()` 返回的不受保护 sqlite3_value 对象，包括
> 调用 `sqlite3_value_int()`、`sqlite3_value_text()` 或 `sqlite3_value_bytes()` 等例程，行为不
> 是线程安全的。因此，`sqlite3_column_value()` 接口通常只在应用自定义 SQL 函数或虚拟表的实现
> 中有用，不在顶层应用代码中有用。

这些例程可能尝试转换结果的数据类型。例如，若内部表示是 FLOAT、请求文本结果，则内部用
`sqlite3_snprintf()` 自动执行转换。下表详述应用的转换：

注意：发生类型转换时，先前调用 `sqlite3_column_blob()`、`sqlite3_column_text()` 和/或
`sqlite3_column_text16()` 返回的指针可能失效。以下情况可能发生类型转换和指针失效：

- 初始内容是 BLOB、且调用 `sqlite3_column_text()` 或 `sqlite3_column_text16()`。可能需要在
  字符串末尾添加零结尾符。
- 初始内容是 UTF-8 文本、且调用 `sqlite3_column_bytes16()` 或 `sqlite3_column_text16()`。
  内容必须转换为 UTF-16。
- 初始内容是 UTF-16 文本、且调用 `sqlite3_column_bytes()` 或 `sqlite3_column_text()`。内容
  必须转换为 UTF-8。

UTF-16be 和 UTF-16le 之间的转换总是就地完成、不会使先前指针失效，当然先前指针引用的缓冲区
内容会被修改。其它种类的转换在可能时就地完成，但有时不可能、此时先前指针失效。

最安全的策略是按以下方式之一调用这些例程：

- `sqlite3_column_text()` 后跟 `sqlite3_column_bytes()`
- `sqlite3_column_blob()` 后跟 `sqlite3_column_bytes()`
- `sqlite3_column_text16()` 后跟 `sqlite3_column_bytes16()`

换句话说，应先调用 `sqlite3_column_text()`、`sqlite3_column_blob()` 或
`sqlite3_column_text16()` 把结果强制为所需格式，然后调用 `sqlite3_column_bytes()` 或
`sqlite3_column_bytes16()` 找出结果的大小。不要把 `sqlite3_column_text()` 或
`sqlite3_column_blob()` 的调用与 `sqlite3_column_bytes16()` 的调用混用，也不要把
`sqlite3_column_text16()` 的调用与 `sqlite3_column_bytes()` 的调用混用。

返回的指针在发生上述类型转换之前、或调用 `sqlite3_step()`、`sqlite3_reset()` 或
`sqlite3_finalize()` 之前有效。容纳字符串和 BLOB 的内存空间自动释放。不要把自己从
`sqlite3_column_blob()`、`sqlite3_column_text()` 等返回的指针传入 `sqlite3_free()`。

只要输入参数正确，这些例程只会在格式转换期间发生内存不足错误时失败。只有以下接口子集受内存
不足错误影响：

- sqlite3_column_blob()
- sqlite3_column_text()
- sqlite3_column_text16()
- sqlite3_column_bytes()
- sqlite3_column_bytes16()

若发生内存不足错误，则这些例程的返回值与列包含 SQL NULL 值相同。可通过在获得可疑返回值后、
在同一数据库连接上调用任何其它 SQLite 接口之前立即调用 `sqlite3_errcode()`，区分有效的 SQL
NULL 返回与内存不足错误。

## 数据库文件的底层控制（Low-Level Control Of Database Files）

```
int sqlite3_file_control(sqlite3*, const char *zDbName, int op, void*);
```

`sqlite3_file_control()` 接口对与第二个参数标识的特定数据库关联的 sqlite3_io_methods 对象
的 xFileControl 方法做直接调用。数据库名对主数据库是 "main"、对 TEMP 数据库是 "temp"、或用
ATTACH SQL 命令添加的数据库名（AS 关键字之后出现的名字）。NULL 指针可代替 "main" 引用主
数据库文件。此例程的第三和第四个参数直接传递到 xFileControl 方法的第二和第三个参数。
xFileControl 方法的返回值成为此例程的返回值。

`sqlite3_file_control()` 的少数操作码由 SQLite 核心直接处理、从不调用
sqlite3_io_methods.xFileControl 方法。op 参数的 `SQLITE_FCNTL_FILE_POINTER` 值使指向底层
sqlite3_file 对象的指针被写入第 4 个参数指向的空间。`SQLITE_FCNTL_JOURNAL_POINTER` 工作方式
类似，区别是它返回与日志文件关联的 sqlite3_file 对象、而非主数据库。`SQLITE_FCNTL_VFS_POINTER`
操作码返回文件的底层 sqlite3_vfs 对象指针。`SQLITE_FCNTL_DATA_VERSION` 从 pager 返回数据版本
计数器。

若第二个参数（zDbName）不匹配任何打开的数据库文件名，则返回 `SQLITE_ERROR`。此错误码不被
记住、`sqlite3_errcode()` 或 `sqlite3_errmsg()` 不会重新调用它。底层 xFileControl 方法也可能
返回 `SQLITE_ERROR`。无法区分错误的 zDbName 与底层 xFileControl 方法返回的 `SQLITE_ERROR`。

另见：文件控制操作码

## 创建或重定义 SQL 函数（Create Or Redefine SQL Functions）

```
int sqlite3_create_function(
  sqlite3 *db,
  const char *zFunctionName,
  int nArg,
  int eTextRep,
  void *pApp,
  void (*xFunc)(sqlite3_context*,int,sqlite3_value**),
  void (*xStep)(sqlite3_context*,int,sqlite3_value**),
  void (*xFinal)(sqlite3_context*)
);
int sqlite3_create_function16(
  sqlite3 *db,
  const void *zFunctionName,
  int nArg,
  int eTextRep,
  void *pApp,
  void (*xFunc)(sqlite3_context*,int,sqlite3_value**),
  void (*xStep)(sqlite3_context*,int,sqlite3_value**),
  void (*xFinal)(sqlite3_context*)
);
int sqlite3_create_function_v2(
  sqlite3 *db,
  const char *zFunctionName,
  int nArg,
  int eTextRep,
  void *pApp,
  void (*xFunc)(sqlite3_context*,int,sqlite3_value**),
  void (*xStep)(sqlite3_context*,int,sqlite3_value**),
  void (*xFinal)(sqlite3_context*),
  void(*xDestroy)(void*)
);
int sqlite3_create_window_function(
  sqlite3 *db,
  const char *zFunctionName,
  int nArg,
  int eTextRep,
  void *pApp,
  void (*xStep)(sqlite3_context*,int,sqlite3_value**),
  void (*xFinal)(sqlite3_context*),
  void (*xValue)(sqlite3_context*),
  void (*xInverse)(sqlite3_context*,int,sqlite3_value**),
  void(*xDestroy)(void*)
);
```

这些函数（统称"函数创建例程"）用于添加 SQL 函数或聚合、或重定义现有 SQL 函数或聚合的行为。
三个 "sqlite3_create_function*" 例程之间的唯一区别是：第二个参数（被创建函数的名字）期望的
文本编码、以及应用数据指针是否有析构回调。函数 `sqlite3_create_window_function()` 类似，但
允许用户提供聚合窗口函数所需的额外回调函数。

第一个参数是要添加 SQL 函数的数据库连接。若应用使用多个数据库连接，则应用自定义 SQL 函数必须
分别添加到每个数据库连接。

第二个参数是要创建或重定义的 SQL 函数的名字。名字的长度限于 UTF-8 表示中的 255 字节，不含
零结尾符。注意：名字长度限制以 UTF-8 字节计，而非字符或 UTF-16 字节。任何创建更长名字的尝试
都将导致返回 `SQLITE_MISUSE`。

第三个参数（nArg）是 SQL 函数或聚合接受的参数个数。若此参数为 -1，则 SQL 函数或聚合可接受
0 到 `sqlite3_limit(SQLITE_LIMIT_FUNCTION_ARG)` 设置的限制之间的任意参数个数。若第三个参数
小于 -1 或大于 127，则行为未定义。

第四个参数 eTextRep 指定此 SQL 函数对其参数偏好的文本编码。若函数实现对输入调用
`sqlite3_value_text16le()`，应用应把此参数设为 `SQLITE_UTF16LE`；若实现对输入调用
`sqlite3_value_text16be()`，设为 `SQLITE_UTF16BE`；若使用 `sqlite3_value_text16()`，设为
`SQLITE_UTF16`；否则设为 `SQLITE_UTF8`。同一个 SQL 函数可以使用不同首选文本编码注册多次，
每种编码有不同实现。当同一函数的多个实现可用时，SQLite 将选择涉及最少数据转换的那个。

第四个参数可可选地与 `SQLITE_DETERMINISTIC` OR 起来，表示函数在单个 SQL 语句内给定相同输入
时总是返回相同结果。大多数 SQL 函数是确定性的。内建 random() SQL 函数是不确定性函数的例子。
SQLite 查询规划器能对确定性函数执行额外优化，因此建议在可能处使用 `SQLITE_DETERMINISTIC` 标志。

第四个参数也可可选地包含 `SQLITE_DIRECTONLY` 标志，若存在则阻止函数从视图、触发器、CHECK
约束、生成列表达式、索引表达式或部分索引的 WHERE 子句内部调用。

为最大安全，建议对所有不需要在触发器、视图、CHECK 约束或数据库 schema 其它元素内部使用的应用
自定义 SQL 函数使用 `SQLITE_DIRECTONLY` 标志。此标志尤其建议用于有副作用或泄露内部应用状态的
SQL 函数。没有此标志，攻击者可能能够修改数据库文件的 schema，使其包含以攻击者选择的参数调用
该函数，应用在打开和读取数据库文件时会执行这些调用。

第五个参数是任意指针。函数的实现可以用 `sqlite3_user_data()` 访问此指针。

传给三个 "sqlite3_create_function*" 函数的第六、七、八个参数 xFunc、xStep 和 xFinal，是指向
实现该 SQL 函数或聚合的 C 语言函数的指针。标量 SQL 函数只需要 xFunc 回调的实现；xStep 和
xFinal 参数必须传 NULL 指针。聚合 SQL 函数需要 xStep 和 xFinal 的实现、xFunc 必须传 NULL 指针。
要删除现有 SQL 函数或聚合，三个函数回调都传 NULL 指针。

传给 `sqlite3_create_window_function` 的第六、七、八、九个参数（xStep、xFinal、xValue 和
xInverse）是指向实现新函数的 C 语言回调的指针。xStep 和 xFinal 必须都非 NULL。xValue 和
xInverse 要么都 NULL（此时创建普通聚合函数），要么都非 NULL（此时新函数既可用作聚合、也可
用作聚合窗口函数）。聚合窗口函数实现的更多细节可在此获得。

若 `sqlite3_create_function_v2()` 或 `sqlite3_create_window_function()` 的最后一个参数非 NULL，
则它是应用数据指针的析构函数。函数被删除时（被重载、或数据库连接关闭时）调用析构函数。若对
`sqlite3_create_function_v2()` 的调用失败，也调用析构函数。调用析构回调时，向它传入单个参数，
即 `sqlite3_create_function_v2()` 第五个参数（应用数据指针）的副本。

允许以相同名字、但参数个数或首选文本编码不同，注册同一函数的多个实现。SQLite 将使用与该
SQL 函数使用方式最接近的实现。非负 nArg 参数的函数实现比负 nArg 的实现更匹配。首选文本编码
匹配数据库编码的函数，比编码不同的函数更匹配。编码差异在 UTF16le 和 UTF16be 之间的函数，
比编码差异在 UTF8 和 UTF16 之间的函数更接近。

内建函数可以被新的应用自定义函数重载。

应用自定义函数允许调用其它 SQLite 接口。但此类调用不得关闭数据库连接、也不得终结或重置
函数正在其中运行的预编译语句。
