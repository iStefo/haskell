# Info 2 - Haskell
### Functions
**Prefix binds stronger than infix**: `f a + b == (f a) + b`

**Prefix** can be turned into **Infix**: `5 ´mod´ 3 = mod 5 3`

**Infix** can be turned into **Prefix**: `(+) 1 2 = 1 + 2`

**Recursion** example with pow function:

```hs
pow x 0 = 1
pox x n | n > 0 = x * pow x (n-1)

-- evaluating pow 2 3
pow 2 3 = 2 * pow 2 2
		= 2 * (2 * pow 2 1)
		= 2 * (2 * (2 * pow 2 0))
		= 2 * (2 * (2 * 1))
		= 8
```

**List definition**:

```hs
[1,2] = 1:2:[]
```

List ranges: `[1 .. 3] = [1, 2, 3]`

**Concatenation**: `[1, 2] ++ [3] = [1, 2, 3]`

```hs
(++) :: [a] -> [a] -> [a]
(:)  :: a -> [a] -> [a]
concat :: [[a]] -> [a]
```

**List comprehension** (Generators):

```hs
[ toLower c | c <- "Hello, World!"]
	= "hello, world!"
	
[ x*x | x <- [1 .. 5], odd x, x > 2]
	= [9, 25]
```

Type variables start with a lower-case letter. (typically a, b, c...)

```hs
head, last :: [a] -> a
	=> head "list" = 'l', last "list" = 't'

tail, init :: [a] -> [a]
	=> tail "list" = "ist", init "list" = "lis"

take, drop :: Int -> [a] -> [a]
	=> take 3 "list" = "lis", drop 3 "list" = "t"

concat :: [[a]] -> [a]
	=> concat [[1, 2], [3, 4], [0]] = [1, 2, 3, 4, 0]

zip :: [a] -> [b] -> [(a,b)]
zip (x:xs) (y:ys) = (x,y) : zip xs ys
zip _ _ = []
	=> zip [1,2] "ab" = [(1, 'a'), (2, 'b')]

unzip :: [(a,b)] -> ([a],[b])
	=> unzip [(1, 'a'), (2, 'b')] = ([1,2], "ab")
```

**Polymorphism**: one definition, many types

**Overloading**: different definition for different types

```hs
filter :: (a -> Bool) -> [a] -> [a]
```

### Structural induction on lists
To prove Property `P(xs)` for all finite lists xs:

* *Base case*: Prove `P([])` and
* *Induction step*: Prove `P(xs)` implies `P(x:xs)`

Example: reverse of ++

**Lemma**: reverse(xs ++ ys) = reverse ys ++ reverse xs

**Proof**:

*Base case:*:

```hs
reverse ([] ++ ys)
= reverse ys ++ reverse []
= reverse ys
```

```hs
reverse ys ++ reverse []
= reverse ys ++ []
= reverse ys
```

*Induction Step*: (to show: `reverse((x:xs)++ys) = reverse ys ++ reverse(x:xs)`)

```hs
reverse ((x:xs) ++ ys)
= reverse (x : (xs ++ ys))
= reverse (xs ++ ys) ++ [x]
= (reverse ys ++ reverse xs) ++ [x]
= reverse ys ++ (reverse xs ++ [x])
```

```hs
reverse ys ++ reverse(x:xs)
= reverse ys ++ (reverse xs ++ [x])
```


### Termination
> A function `f :: T1 -> T` terminates if there is a *mesasure function* `m :: T1 -> N` such that

> * for every defining equation `f p = t`
* and for every recursive call `f r` in t:
* `m p > m r`

### Higher-Order functions
map:

```hs
map :: (a -> b) -> [a] -> [b]

length (map f xs) = length xs

map f (xs ++ ys) = map f xs ++ map f ys

map f (reverse xs) = reverse (map f xs)
```

filter:

```hs
filter :: (a -> Bool) -> [a] -> [a]
```

foldr:

```hs
foldr :: (a -> a -> a) -> a -> [a] -> a
foldr f a []     = a
foldr f a (x:xs) = x ´f´ foldr f a xs

-- examples
product xs = foldr (*)  1     xs
and xs     = foldr (&&) True  xs
or xs      = foldr (||) False xs
inSort xs  = foldr ins  []    xs
```

### Lamdba expressions
```hs
(+ 1) = (\x -> x + 1)
(2 *) = (\x -> 2 * x)
(2 ^) = (\x -> 2 ^ x)
(^ 2) = (\x -> x ^ 2)
```

### Curried functions
> Any function of two arguments can be viewed as a function of the first argument that returns a function of the second argument.

```hs
f :: Int -> Int -> Int   => f x y = x+y
f :: Int -> (Int -> Int) => f x = \y -> x+y

const :: a -> (b -> a)
const x = \ x -> x

curry :: ((a,b) -> c) -> (a -> b -> c)
curry f = \ x y -> f(x,y)

uncurry :: (a -> b -> c) -> ((a,b) -> c)
uncurry f = \ (x,y) -> f x y
```

### Function composition
Build new functions (lambda expressions) by composing two existing functions:

```hs
(.) :: (b -> c) -> (a -> b) -> a -> c  
f . g = \x -> f (g x)

-- example
head2 = head . tail
head2 [1,2,3]
= (head . tail) [1,2,3]
= (\x -> head (tail x)) [1,2,3]
= head (tail [1,2,3])
= head [2,3] = 2
```

### Type classes
> Type classes are collections of types that implement some fixed set of functions

```hs
class Eq a where
	(==) :: a -> a -> Bool
	
-- general
class C a where
	f1 :: T1
	fn :: Tn
```

> A type T is an instance of class C if T supports all the functions of C.  
> Then we write C T, e.g. `Eq Int`

Make a type an instance of a class:

```hs
instance Eq Bool where
	True == True	= True
	False == False	= True
	_ == _			= False
```

### Data Types
Example from the Prelude:

```hs
data Bool = False | True
	deriving (Eq, Show) -- let haskell write code for you

not :: Bool -> Bool
not False = True
not True = False

(&&) :: Bool -> Bool -> Bool
False && _ = False
True  && q = q

(||) :: Bool -> Bool -> Bool
False || q = q
True  || _ = True
```

#### Trees
Tree structure with data in nodes:

```hs
data Tree a = Empty | Node a (Tree a) (Tree a)
	deriving (Eq, Show)

find :: Ord a => a -> Tree a -> Bool
find _ Empty  =  False
find x (Node a l r)
	| x<a = find x l
	| a<x = find x r
	| otherwise = True

insert :: Ord a => a -> Tree a -> Tree a
insert x Empty  =  Node x Empty Empty
insert x (Node a l r)
	| x<a = Node a (insert x l) r
	| a<x = Node a l (insert x r)
	| otherwise = Node a l r
```

### Structural induction for Tree
```hs
data Tree a = Emtpy | Node a (Tree a) (Tree a)
```

