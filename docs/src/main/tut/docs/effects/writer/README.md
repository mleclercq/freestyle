---
layout: docs
title: Writer
permalink: /docs/effects/writer/
---

## WriterM

The writer effect allows for the accumulation of values, which can be obtained once the program is interpreted.

The `writer` effect supports parameterization to any type that supports monoidal accumulation while remaining type safe throughout the program declaration. 

There needs to be implicit evidence of `cats.mtl.FunctorTell[M, W]` and `Monad[M]`
for any runtime `M[_]` used in its interpretation due to the constraints placed by this effect. 

The writer effect comes with two operations `writer` and `tell`.

### writer

`writer` sets a tuple with the current accumulator value and returning value:

```tut:book
import freestyle.free._
import freestyle.free.implicits._
import freestyle.free.effects.writer
import cats.data.Writer
import cats.implicits._
import cats.mtl.implicits._

val wr = writer[List[Int]]

import wr.implicits._

type Logger[A] = Writer[List[Int], A]

def programWriter[F[_]: wr.WriterM] =
  for {
    _ <- FreeS.pure(1)
    b <- wr.WriterM[F].writer((Nil, 1))
    _ <- FreeS.pure(1)
  } yield b
  
programWriter[wr.WriterM.Op].interpret[Logger].run
```

### tell

`tell` appends a value for monoidal accumulation:

```tut:book
def programTell[F[_]: wr.WriterM] =
  for {
    _ <- FreeS.pure(1)
    b <- wr.WriterM[F].writer((List(1), 1))
    c <- wr.WriterM[F].tell(List(1))
    _ <- FreeS.pure(1)
  } yield b
      
programTell[wr.WriterM.Op].interpret[Logger].run
```
