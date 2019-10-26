# winpid

Pid file library that works on both Windows an Linux. It does not lock the pid file and it does not use the fnctl
module. Instead of locking, it relies on the existence of the file. Under normal conditions, the pid file is
automatically deleted when the process exists. Because of the unhandled race condition, it is not safe to use for
processes may be started at the exact same moment. But for most long running server processes, it can be used safely
on many platforms.

## Basic usage

    from winpid import create_pid_file_or_exit
    create_pid_file_or_exit("my_pid_file.pid")
    main()

Please note that you do not need to delete the pid file manually. The above code will print an error message to
stderr if there is another process with the same pid file.

## Advanced usage

### Execute your own code when already running

    from winpid import create_pid_file, AlreadyRunningError
    try:
        create_pid_file(pid_file_path="/var/run/my_process.pid")
    except AlreadyRunningError as e:
        print(e.other_pid)
    else:
        main()

### Silently abort your process when already running

    from winpid import create_pid_file
    create_pid_file(pid_file_path="/var/run/my_process.pid", silent_abort=True)
    main()

This will raise SystemExit(0) when already running.

### Remove pid file forcedly

    #args = argparse.parse()...
    from winpid import create_pid_file
    create_pid_file(pid_file_path="/var/run/my_process.pid", auto_remove_pid_file=args.auto_remove_pid_file)
    main()

This version is useful for debugging. E.g. if you are testing your code from a debugger, then aborting the program
from the debugger may result in a leftover pid file. You can use a command line argument to remove the pid file.
It makes debugging easier, while not affecting normal users.