# ansible-lint-crasher
This is a minimal reproduction of a crash affecting `ansible-lint` described in issue [#3612](https://github.com/ansible/ansible-lint/issues/3612).
It assumes that you are using a distribution of Linux, but this may trigger the same crash in other operating systems.
The cause of the crash appears to be the 

1. Install `ansible-lint` by creating a virtual environment:

```sh
python3 -m venv .venv
```

2. Enter the virtual environment:

```sh
source .venv/bin/activate
```

3. Install `ansible-lint`:

```sh
pip install ansible-lint
```

4. Run `ansible-lint` on the `common` role's task file:

```sh
ansible-lint --offline -f codeclimate roles/common/tasks/main.yml
```

5. Observe the crash:

```raw
Traceback (most recent call last):
  File "/home/user/projects/ansible-lint-crasher/.venv/bin/ansible-lint", line 8, in <module>
    sys.exit(_run_cli_entrypoint())
             ^^^^^^^^^^^^^^^^^^^^^
  File "/home/user/projects/ansible-lint-crasher/.venv/lib/python3.12/site-packages/ansiblelint/__main__.py", line 408, in _run_cli_entrypoint
    sys.exit(main(sys.argv))
             ^^^^^^^^^^^^^^
  File "/home/user/projects/ansible-lint-crasher/.venv/lib/python3.12/site-packages/ansiblelint/__main__.py", line 360, in main
    result = get_matches(rules, options)
             ^^^^^^^^^^^^^^^^^^^^^^^^^^^
  File "/home/user/projects/ansible-lint-crasher/.venv/lib/python3.12/site-packages/ansiblelint/runner.py", line 679, in get_matches
    matches.extend(runner.run())
                   ^^^^^^^^^^^^
  File "/home/user/projects/ansible-lint-crasher/.venv/lib/python3.12/site-packages/ansiblelint/runner.py", line 156, in run
    matches = self._run()
              ^^^^^^^^^^^
  File "/home/user/projects/ansible-lint-crasher/.venv/lib/python3.12/site-packages/ansiblelint/runner.py", line 212, in _run
    if isinstance(lintable.data, States) and lintable.exc:
                  ^^^^^^^^^^^^^
  File "/home/user/projects/ansible-lint-crasher/.venv/lib/python3.12/site-packages/ansiblelint/file_utils.py", line 434, in data
    self.state = append_skipped_rules(
                 ^^^^^^^^^^^^^^^^^^^^^
  File "/home/user/projects/ansible-lint-crasher/.venv/lib/python3.12/site-packages/ansiblelint/skip_utils.py", line 113, in append_skipped_rules
    yaml_skip = _append_skipped_rules(pyyaml_data, lintable)
                ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
  File "/home/user/projects/ansible-lint-crasher/.venv/lib/python3.12/site-packages/ansiblelint/skip_utils.py", line 202, in _append_skipped_rules
    for ruamel_task, pyyaml_task in zip(ruamel_tasks, pyyaml_tasks, strict=False):
  File "/home/user/projects/ansible-lint-crasher/.venv/lib/python3.12/site-packages/ansiblelint/skip_utils.py", line 251, in _get_tasks_from_blocks
    yield from get_nested_tasks(task)
  File "/home/user/projects/ansible-lint-crasher/.venv/lib/python3.12/site-packages/ansiblelint/skip_utils.py", line 240, in get_nested_tasks
    if not task or not is_nested_task(task):
                       ^^^^^^^^^^^^^^^^^^^^
  File "/home/user/projects/ansible-lint-crasher/.venv/lib/python3.12/site-packages/ansiblelint/skip_utils.py", line 314, in is_nested_task
    return any(task.get(key) for key in NESTED_TASK_KEYS)
           ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
  File "/home/user/projects/ansible-lint-crasher/.venv/lib/python3.12/site-packages/ansiblelint/skip_utils.py", line 314, in <genexpr>
    return any(task.get(key) for key in NESTED_TASK_KEYS)
               ^^^^^^^^
AttributeError: 'CommentedSeq' object has no attribute 'get'
```

