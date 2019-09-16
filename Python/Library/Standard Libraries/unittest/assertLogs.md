# assertLogs
`unittest.TestCase.assertLogs` 메소드를 통해 logger의 output을 assertion할 수 있다.

```python
import logging

logger = logging.getLogger('foo')

from unittest import TestCase


def log(msg):
    logger.info(msg)


class TestLog(TestCase):
    def test(self):
        with self.assertLogs('foo', level='INFO') as cm:
            log('abc')
            self.assertEqual(cm.output, ['INFO:foo:abc'])


TestLog().test()
```
