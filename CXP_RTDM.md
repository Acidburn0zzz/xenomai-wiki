# CXP-compliant RTDM - DRAFT PROPOSAL

| RTDM base types | Present in CXP | EVL transposition  |
| :-- |:-- |:-- |
| rtdm_driver      | Yes |  rtdm_driver |
| rtdm_device      | Yes |  rtdm_device |
| rtdm_dev_context | Yes | rtdm_dev_context, tracked by filp->private_data |
| rtdm_fd          | Yes | rtdm_fd, backpoints to struct file *filp |

| rtdm_fd accessors  | Present in CXP | EVL transposition  |
| :-- | :-- |:-- |
| rtdm_fd_to_context(fd) | Yes |   specific   |
| rtdm_fd_to_private(fd) | Yes | specific |
| rtdm_private_to_fd(fd) | Yes | specific |
| rtdm_fd_is_user(fd)    | Unsure. Do we still have actual use cases for the kernel->kernel API? | specific |
| rtdm_fd_device(fd)     | Yes | specific  |

| Driver registration  | Present in CXP | EVL transposition  |
| :-- | :-- |:-- |
| rtdm_dev_register()   | Yes | regular cdev setup encapsulation   |
| rtdm_dev_unregister() | Yes | regular cdev cleanup encapsulation |
| RTDM_PROFILE_INFO()   | Yes | same |

| Clock services      | Present in CXP | EVL transposition   |
| :-- |  :-- |:-- |
| rtdm_clock_read()           | Yes | evl_read_clock(&evl_mono_clock) |
| rtdm_clock_read_monotonic() | Yes | evl_read_clock(&evl_realtime_clock) |

|     Timeout sequence      | Present in CXP | EVL transposition   |
| :-- |  :-- |:-- |
| rtdm_toseq_init()           |      Yes        | specific |

|     Locking      | Present in CXP | EVL transposition   |
| :-- |  :-- |:-- |
| rtdm_lock_init()           |      Yes        | Dovetail hard_spinlock API encapsulation |
| rtdm_lock_get()           |      Yes        | Dovetail hard_spinlock API encapsulation |
| rtdm_lock_get_irqsave()           |      Yes        | Dovetail hard_spinlock API encapsulation |
| rtdm_lock_put()           |      Yes        | Dovetail hard_spinlock API encapsulation |
| rtdm_lock_put_irqrestore()           |      Yes        |  Dovetail hard_spinlock API encapsulation |
| RTDM_EXECUTE_ATOMICALLY()           |      No, EVL has no single big lock. Must be converted to resource-specific locking   | x |
| cobalt_atomic_enter()           |      No, same as RTDM_EXECUTE_ATOMICALLY()   | x |
| cobalt_atomic_leave()           |      No, same as RTDM_EXECUTE_ATOMICALLY()   | x |

|     Wait queues      | Present in CXP | EVL transposition   |
| :-- |  :-- |:-- |
| rtdm_waitqueue_init()  | Yes | evl_init_wait() |
| rtdm_waitqueue_destroy()  | Yes | evl_destroy_wait() |
| rtdm_waitqueue_lock()   | Yes | Dovetail hard_spinlock API encapsulation |
| rtdm_waitqueue_unlock()  | Yes |Dovetail hard_spinlock API encapsulation |
| rtdm_waitqueue_signal()  | Yes | evl_wake_up() |
| rtdm_waitqueue_wakeup()  | Yes | evl_wake_up() |
| rtdm_waitqueue_broadcast()  | Yes | evl_flush_wait() |
| rtdm_timedwait_condition()  | Yes | evl_wait_event_timeout() |
| rtdm_timedwait_condition_locked()  | Yes  | evl_add_wait_queue(), evl_wait_schedule() |
| rtdm_wait_condition()  | yes | evl_wait_event() |
| rtdm_wait_condition_locked()  | Yes  | evl_add_wait_queue(), evl_wait_schedule() |
| rtdm_wait()  | No, wait queues must be paired with conditions, e.g. flag | x |

|     IRQ management      | Present in CXP | EVL transposition   |
| :-- |  :-- |:-- |
| rtdm_irq_get_arg()  | Yes | specific |
| rtdm_irq_request()  | Yes | request_irq() encapsulation |
| rtdm_irq_free()  | Yes | free_irq() encapsulation |
| rtdm_irq_enable()  | Yes | enable_irq() encapsulation |
| rtdm_irq_disable()  | Yes | disable_irq() encapsulation |

|     In-band work scheduling      | Present in CXP | EVL transposition   |
| :-- |  :-- |:-- |
| rtdm_nrtsig_init()        |  Yes          |  irq_work |
| rtdm_nrtsig_destroy()     |  Yes          |  irq_work |
| rtdm_nrtsig_pend()     |  Yes          |  irq_work |
| rtdm_schedule_nrt_work()     |  Yes          |  evl_work |

|     Timer management      | Present in CXP | EVL transposition   |
| :-- |  :-- |:-- |
| rtdm_timer_init() | Yes | evl_init_timer() |
| rtdm_timer_destroy() | Yes | evl_destroy_timer() |
| rtdm_timer_start() | Yes | evl_start_timer() |
| rtdm_timer_stop() | Yes | evl_stop_timer() |
| rtdm_timer_start_in_handler() | Yes | evl_start_timer() |
| rtdm_timer_stop_in_handler() | Yes | evl_stop_timer() |

|     Task management      | Present in CXP | EVL transposition   |
| :-- |  :-- |:-- |
| rtdm_task_init() | Yes | evl_run_kthread() |
| rtdm_task_destroy() | Yes | evl_stop_kthread() |
| rtdm_task_join() | Yes | evl_join_kthread() |
| rtdm_task_busy_sleep() | Yes | evl_run_kthread() |
| rtdm_task_should_stop() | Yes | evl_kthread_should_stop() |
| rtdm_task_set_priority() | Yes | evl_set_kthread_priority() |
| rtdm_task_set_period() | Yes | evl_set_period() |
| rtdm_task_unblock() | Yes | evl_unblock_kthread() |
| rtdm_task_current() | Yes | evl_current() |
| rtdm_task_wait_period() | Yes | evl_wait_period() |
| rtdm_task_sleep() | Yes | evl_sleep() |
| rtdm_task_sleep_abs() | Yes | evl_sleep_until() |
| rtdm_task_busy_wait() | Yes | specific |

|     Event flags      | Present in CXP | EVL transposition   |
| :-- |  :-- |:-- |
| rtdm_event_init() | Yes | evl_init_flag() |
| rtdm_event_select() | Yes | evl_poll_watch() |
| rtdm_event_wait() | Yes | evl_wait_flag() |
| rtdm_event_timedwait() | Yes | evl_wait_flag_timeout() |
| rtdm_event_signal() | Yes | evl_raise_flag() |
| rtdm_event_clear() | Yes | evl_clear_flag() |
| rtdm_event_pulse() | Yes | evl_pulse_flag() |
| rtdm_event_destroy() | Yes | evl_destroy_flag() |

|     Semaphore      | Present in CXP | EVL transposition   |
| :-- |  :-- |:-- |
| rtdm_sem_init() | Yes | evl_init_ksem() |
| rtdm_sem_select() | Yes | evl_poll_watch() |
| rtdm_sem_down() | Yes | evl_down() |
| rtdm_sem_timeddown() | Yes | evl_down_timeout() |
| rtdm_sem_up() | Yes | evl_up() |
| rtdm_sem_destroy() | Yes | evl_destroy_ksem() |

|     Mutex      | Present in CXP | EVL transposition   |
| :-- |  :-- |:-- |
| rtdm_mutex_init() | Yes | evl_init_kmutex() |
| rtdm_mutex_lock() | Yes | evl_lock_kmutex() |
| rtdm_mutex_timedlock() | Unsure. Timeout would indicate basic program dysfunction, better introduce rtdm_mutex_trylock(). | x |
| rtdm_mutex_unlock() | Yes | evl_unlock_kmutex() |
| rtdm_mutex_destroy() | Yes | evl_destroy_kmutex() |

|     Memory allocation      | Present in CXP | EVL transposition   |
| :-- |  :-- |:-- |
| rtdm_malloc() | Yes | evl_alloc_chunk() |
| rtdm_free() | Yes | evl_free_chunk() |

|     Memory mapping      | Present in CXP | EVL transposition   |
| :-- |  :-- |:-- |
| rtdm_mmap_to_user() | No [obsolete], to be converted to rtdm_mmap_kmem(), rtdm_mmap_vmem() | x |
| rtdm_iomap_to_user() | No [obsolete], to be converted to rtdm_mmap_iomem() | x |
| rtdm_mmap_kmem() | Yes | specific |
| rtdm_mmap_vmem() | Yes | specific |
| rtdm_mmap_iomem() | Yes | specific |
| rtdm_munmap() | No [obsolete], paired wth rtdm_mmap_to_user(), rtdm_iomap_to_user() | x |

|     User memory accessors      | Present in CXP | EVL transposition   |
| :-- |  :-- |:-- |
| rtdm_read_user_ok() | No, duplicates check in rtdm_copy_from_user() | x |
| rtdm_rw_user_ok() | No, duplicates check in rtdm_copy_from_user(), rtdm_copy_to_user() | x |
| rtdm_copy_from_user() | Yes | raw_copy_from_user() |
| rtdm_safe_copy_from_user() | No, misnamed and sloppy: rtdm_copy_from_user() is safe already provided the status is duly tested. | x |
| rtdm_copy_to_user() | Yes | raw_copy_to_user() |
| rtdm_safe_copy_to_user() | No, misnamed and sloppy: rtdm_copy_to_user() is safe already provided the status is duly tested. | x |
| rtdm_strncpy_from_user() | No, should be reserved to creation calls running in-band, for which we have getname() | x |

|     Ancillaries      | Present in CXP | EVL transposition   |
| :-- |  :-- |:-- |
| rtdm_rt_capable() | Yes | running_inband(), evl_current() |
| rtdm_in_rt_context() | Yes | running_oob() |
| rtdm_drv_set_sysclass()     |   No, core-specific     |   x |
| rtdm_dev_to_kdev()          |   No, core-specific     |   x |
| rtdm_printk() | Yes | printk() |
| rtdm_printk_ratelimited() | Yes | specific |
| rtdm_wait_prepare()  | No, core-specific | x |
| rtdm_wait_complete()  | No, core-specific | x |
| rtdm_wait_get_context()  | No, core-specific | x |
| rtdm_wait_is_completed()  | No, core-specific | x |
