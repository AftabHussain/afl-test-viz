# afl-test-viz
*A project on visualizing tests generated in AFL during fuzzing.*

## Overview
In this work, we provide a way to visualize tests that are generated during fuzzing by AFL, at the byte-level. The visualization helps us see which bytes of the testcase AFL is performing mutations upon. 

## Components

1. **Testcase byte code generator** - This component captures the byte stream representation of tests generated by AFL through mutation and saves them in a single file (`tests_generated`) as hex code. This file is generated in the AFL output folder. Each line of this file corresponds to the byte representation of a single test case. It is implemented as a patch in AFL (`afl-fuzz.c`). The patch is provided below which is added in the `common_fuzz_stuff` function, right before the line where `write_to_testcase` is invoked.:

```
  u8* fn = alloc_printf("%s/tests_generated", out_dir);
  FILE* f = fopen(fn, "a");

  ck_free(fn);

  // Write the test cases in bytes.
  for (int i=0; i<len; i++) {
    fprintf(f, "%02x ", out_buf[i]);
  }
  
  fprintf(f,"\n");
  fclose(f);
```

2. **Testcase image generator** -  This component reads the file generated by the byte code generator, and generates a image file (in `png` format) for each test case. In an image, each box represents a byte of a test case. The colors of the boxes are obtained from the hex value of each byte of the input test in the following way: if a byte is, say, `7c`, then the hex color code is `#7c0000`.

## Sample image representation of a testcase
![sample-test-image](https://github.com/AftabHussain/afl-test-viz/blob/main/figs/test-bytes.png)

