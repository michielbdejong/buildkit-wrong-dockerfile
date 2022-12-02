# Docker BuildKit using wrong dockerfile

OS: Ubuntu Linux 18.04

Docker version: Docker version 19.03.6, build 369ce74a3c

## Reproduction

Clone this repo.

Run

```bash
./test.sh
```

When building the second image, it will give an error

```
 => ERROR [3/3] COPY ./a/test.txt ./                                                                                                                                                             0.0s
------
 > [3/3] COPY ./a/test.txt ./:
------
failed to solve with frontend dockerfile.v0: failed to build LLB: failed to compute cache key: "/a/test.txt" not found: not found
```

which doesn't make sense because that's a line from the first dockerfile.
The reason for failure is that the second image uses a different ignore file, which doesn't provide `a/test.txt`.

## Additional information

* My guess is that it has something to do with the caching of BuildKit.

* Occasionally, it will not give the error and succeed at random...

* It appears that the error/success is determined for each folder, meaning if you don't remove the `.test` folder,
  it will consistently fail or succeed.

* It appears that it only happens when the two dockerfiles have the exact same size.

* The line that says `=> transferring dockerfile` have different sizes when fail vs succeed.
  For me it is `111B` (fail) vs `175B` (succeed).

* The same thing happens for ignore files as well. Right now the two ignore files are different by 1 byte (one `\n`).
  If they are the same size, when building the second image it will actually use both the first dockerfile and the first ignore file,
  resulting in an image that is identical to the first image.
  
 * See also [this alternative demonstration](https://github.com/michielbdejong/codespaces-weirdness/blob/main/README.md#codespaces-weirdness) that has some additional info.
