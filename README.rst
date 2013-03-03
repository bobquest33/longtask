longtask
--------

Long task runner with few nice features.

``longtask`` is a simple framework to run long tasks (eg. data migration, data processing) in console environment.
During process execution you can track changes on progress bar. If something goes wrong or you don't have time now - you
can stop task and rerun it later. ``longtask`` collects all your task stats and store it in JSON file.

Example
-------

``resize.py`` - resize many pictures:

::

    import longtask
    import glob
    from PIL import Image


    class ResizeTask(longtask.Task):
        name = 'resize'

        def get_items(self):
            return glob.glob('*.png')

        def process_item(self, item):
            original_image = Image.open(item)
            resized_image = original_image.resize((100, 100))
            resized_image.save('output/' + item)


    if __name__ == '__main__':
        ResizeTask().run()

Now in directory with ``*.png`` images you can run:

::

    $ python resize.py
    [*] Starting task: resize [2013-03-03 18:36:29.953306]
    [*] Processed: 100% (1000/1000) errors:   10% (100/1000) |#####################| Elapsed Time: 1:00:00 ETA:  1:00:00
    [*] Finished task: resize [2013-03-03 18:36:30.080421]
    [*] Stats:
      - processed: 100% (1000/1000)
      - success: 90% (900/1000)
      - errors: 10% (100/1000)
        - 100 x IOError with traceback:

    Traceback (most recent call last):
      File "/Users/konradhalas/dev/workspace/personal/longtask/longtask/task.py", line 59, in run
        self.process_item(item)
      File "resize.py", line 13, in process_item
        original_image = Image.open(item)
      File "/Users/konradhalas/dev/virtualenvs/longtask/lib/python2.7/site-packages/PIL/Image.py", line 1980, in open
        raise IOError("cannot identify image file")
    IOError: cannot identify image file

In our example we resized 1000 files - 100 of them have corrupted data (``IOError``). This task took 1h.

You can always stop task with ``Ctrl+C`` and rerun it later with flag ``-c``. If some errors occured durring execution
you can rerun it with flag ``-e`` (it works only with flag ``-c``).