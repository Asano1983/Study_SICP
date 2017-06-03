
���Ђ̗v��ł͂Ȃ��āA�������v�������Ƃ��L�^����B

# 1 �葱���ɂ�钊�ۂ̍\�z

�����͓���B
�f�[�^�ƃv���Z�X�Ƃ������̂�����炵���B
�f�[�^�͂Ȃ�ƂȂ��킩�邪�A�v���Z�X�Ƃ͂Ȃ񂼂�B

�v���O��������ŋL�q����鉽�炩�́u�葱���v�ŁA�C���^�[�v���^�ŏ��������O�̂��̂ł���炵�����A
�����ƒ�`�������Ă���킯�ł͂Ȃ��B

�a�c��ł̓v���Z�X�̎葱���Ƃ����p�ꂪ�ł����肵�āA���������Ă���̂��킩��Ȃ������̂ŁA������ǂ񂾂Ƃ���

The most significant of these features is the fact that Lisp descriptions of processes, called procedures, can themselves be represented and manipulated as Lisp data.

�Ə����Ă����āA�v���Z�X��Lisp�ŋL�q�������̂��uprocedure�v�ƌĂсAprocesure��Lisp�̃f�[�^�Ƃ��ĕ\�������葀�삵����ł���Ƃ������Ƃ炵���B

## 1.1 �v���O�����̗v�f

### 1.1.1 ��

Scheme�ł͑S�đO�u�L�@����B

�����b�g

- �C���^�[�v���^�̎������e�Ձi�\���؂��̂܂܁j�B
- ���Z�q�̗D�揇�ʂ����߂ĂȂ��Ă悢�B

�f�����b�g

- �J�b�R���炯�ɂȂ�B

���܂��C���f���g����΁A�ǐ��͂����܂łЂǂ����Ƃɂ͂Ȃ�Ȃ��悤���B

```Scheme
(+ (* 3
      (+ (* 2 4)
         (+ 3 5)))
   (+ (- 10 7)
      6))
```

���̂悤�ɔ퉉�Z�q�������ɐ��񂷂�悤�ɏ������@��pretty print�Ƃ����B

�������z���g�ɍ\���؂��̂܂܂Ȃ̂ˁB
�l�Ԃ��C���^�[�v���^�ɗD��������Ɍ�����B

### 1.1.2 ���O�Ɗ�

����`��define���o��B
define����Ɩ��O�ƃI�u�W�F�N�g�̑΂��u���v�ɓo�^�����B
���O�ƃI�u�W�F�N�g�͑Ή����Ă��邾���œ������̂ł͂Ȃ����Ƃɒ��ӁB

### 1.1.3 �g�����̕]��

(���Z�q �퉉�Z�q �퉉�Z�q �퉉�Z�q)�݂����ȕ�������u�g�����v�Ƃ����炵���B
define�͓���`���ł����āA���Z�q�ł͂Ȃ��̂ŁA
(define x 3)�͑g�����ł͂Ȃ��B

�g�����̕]���́A���Z�q��퉉�Z�q�i�����j�����ɍ�p������Ƃ������̂ł���B
����`���͂����������̂ł͂Ȃ��i��̗�ł�����define��x��3�ɍ�p����킯�ł͂Ȃ��j�B

### 1.1.4 �����葱��

```Scheme
(define (square x) (* x x))
```

�̂悤�Ƀ��[�U�[��`���ꂽ���Z�q�������葱���Ƃ����B
�i��x��`���Ă��܂��΁j�����葱���Ƒg�ݍ��݂̎葱������ʂ���Ӗ��͂Ȃ������ł���B

Scheme�͐��Ɗ֐��̖��O��Ԃ������ł��邪�iLisp-1�j�A����0�����֐��͋�ʂ���Ă���悤�ł���B

```
> (define a 2)
> a
2
> (define (f) 2)
> f
#<procedure:f>
```

### 1.1.5 �葱����p�̒u�������f��

���p�����[�^��l�ɒu�������Ă����菇�̂��Ƃ�u�������f���Ƃ����B
�l�Ԃ��葱����p���l����Ƃ��̃��f���ł����āA�C���^�[�v���^�̎��ۂ̓�����������邽�߂̂��̂ł͂Ȃ�
�i�C���^�[�v���^�͕����񑀍������킯�ł͂Ȃ��j�B

���Z�q���ɓW�J���Ă���A��ŊȖ񂷂���@�𐳋K�����̕]���Ƃ����A
��Ɉ�����]�����Ă����p��������@����p�I�����̕]���Ƃ����B
Lisp�͍�p�I�����̕]�����g���Ă��邪�A�x���X�g���[���Ȃǂ̗����ɂ����Ă͐��K�����̕]�����d�v�ł���炵���i3�͂�4�͂ň����j�B

���K�����̕]���͍ŊO�ō��Ȗ�▼�O�ĂтƂ������B
���̕]���헪�͓��������𕡐���]�����Ă��܂��Ƃ������\��̌��_������B
���p�I�Ȍ���ō̗p����邱�Ƃ͂Ȃ��͂��ł���B

Haskell�̕]���헪�͕K�v�Ăсicall by need�j�ƌ����āA�����]�������������Ēu���A���������𕡐���]�����Ȃ��悤�ɂȂ��Ă���B
����p���Ȃ���΁A�K�v�Ăтɂ��v�Z���ʂ͐��K�����̕]���̌v�Z���ʂƈ�v���邪�A
����p������΂��̌���ł͂Ȃ��iHaskell�͕���p�֎~�Ȃ̂ő��v�j�B

### 1.1.6 �������Əq��

cond�Aif�Aand�Aor�Anot�Ȃǂ̏Љ�B
cond�Aif�Aand�Aor�͒Z���]�����K�v�Ȃ��ߓ���`���ł��邪�Anot�͒ʏ�̉��Z�q�ł���B

#### ���1.1

�����Z�⊄��Z���R�ȏ�̈���������炵���B

```
> (- 9 1 2)
6
> (/ 36 2 3)
6
```

#### ���1.5

```Scheme
(define (p) (p))

(define (test x y)
  (if (= x 0)
      0
      y))
```

p�͎������g�ōċA�I�ɒ�`����Ă��āAp��]������ƌv�Z����~���Ȃ��i�v�Z�@�Ȋw�ł����ہj�B
�������A�C���^�[�v���^�����K�����̕]���ɏ]���̂ł���΁A

```Scheme
(test 0 (p))
```

�͒�~����ip��]�����Ȃ��̂Łj�B
��p�I�����̕]���ɏ]���̂Ȃ�Β�~���Ȃ��B
���̂��߁A���̃e�X�g�ɂ���āA�C���^�[�v���^�����K�����ł��邩��p�I�����ł��邩�𔻒肷�邱�Ƃ��ł���B

Haskell�iGHCi�j�Ŏ����Ă݂��B

```
Prelude> let p = p
Prelude> let test x y = if x == 0 then 0 else y
Prelude> test 0 p
0
```

�����ƒ�~����B

### 1.1.7 Newton�@�ɂ�镽����

�ċA���g����Newton�@����������b�B
�{���ɂ́u���[�v�\�����Ȃ��Ă������邩�炷�����I�v���ď����Ă��邪�A�ċA�̓��[�v��苭�͂Ȃ̂œ�����O�ȋC������B

```Scheme
(define (square x) (* x x))

(define (sqrt-iter guess x)
  (if (good-enough? guess x)
      guess
      (sqrt-iter (improve guess x)
                 x)))

(define (improve guess x)
  (average guess (/ x guess)))

(define (average x y)
  (/ (+ x y) 2))

(define (good-enough? guess x)
  (< (abs (- (square guess) x)) 0.001))

(define (my-sqrt x)
  (sqrt-iter 1.0 x))
```

�����ł͎����p�֐��isqrt-iter�Ȃǁj�����J����Ă��܂��Ă��邪�A����1.1.8�Ŏ����p�֐����B������B

#### ���1.6

if�͂Ȃ�����`���Ȃ̂��Ƃ����b�B
Scheme�͐��i�]���i�l�Ăсj�Ȃ̂ŁA�ʏ�̊֐��Ƃ��Ē�`���Ă��܂��ƁA
���then�߂�else�߂̗�����]�����Ă��܂��B
�Ⴆ�΁A���sqrt-iter��if���ʏ�̊֐��Ȃ�΁A�v�Z���~�܂�Ȃ��Ȃ��Ă��܂��B

#### ���1.7

���good-enough?��x�����������̎��ɂ͍��邪�A���ɑ傫�Ȑ��ł����Ă�����B

```
> (my-sqrt 1e-10)
0.03125000106562499
```

�������Ƃ��͌덷�̕]�����̂��s�K�؂ł���B

```
> (my-sqrt 1e100)
```

���ɑ傫���Ƃ��́A�ۂߌ덷�̂��ߌv�Z���~�܂�Ȃ��Ȃ��Ă��܂��B

### 1.1.8 �u���b�N�{�b�N�X���ۂƂ��Ă̎葱��

���R�ϐ��Ƒ����ϐ��̘b���������Ə����Ă���B
�����ϐ��i���[�J���ϐ��j�̖��O�����l�[�����Ă��Ӗ��͕ς��Ȃ��B
�ϐ�����������Ă���͈͂��X�R�[�v�Ƃ����A�ȂǁB
���ϊ��̌����Ȓ�`�ɂ��ď����Ă���킯�ł͂Ȃ��B

```Scheme
(define (square x) (* x x))

(define (average x y)
  (/ (+ x y) 2))

(define (my-sqrt x)
  (define (good-enough? guess x)
    (< (abs (- (square guess) x)) 0.001))
  (define (improve guess x)
    (average guess (/ x guess)))
  (define (sqrt-iter guess x)
    (if (good-enough? guess x)
        guess
        (sqrt-iter (improve guess x)
                   x)))
  (sqrt-iter 1.0 x))
```

�Ăяo�����Ɍ�����K�v���Ȃ��֐��̓u���b�N�\�����g���ĉB�����悤�B

���L�V�J���E�X�R�[�s���O�Ƃ����p��͏o�Ă��邪�A�_�C�i�~�b�N�E�X�R�[�s���O�̘b�͖����B
�_�C�i�~�b�N�E�X�R�[�s���O�͖Y��悤�i���ƂȂ��Ă�Emacs Lisp���炢�Ȃ��̂��낤�j�B
�����_�v�Z�͎��R�Ƀ��L�V�J���E�X�R�[�s���O�ł���B

�]�k�F
C++�̂悤�Ȍ���ł������_�����O���̕ϐ��ɑ������ƃX�R�[�v�����G�ɂȂ�B

```C++
#include <functional>

int main()
{
    std::function<int(int)> func;
    
    {
        int a = 3;
        func = [a](int x){return a * x;};
    }
    
    return func(5);
}
```

����͂�����15��߂��Ă���邪�ifunc������a�̓R�s�[�ō����̂Łj

```
#include <functional>

int main()
{
    std::function<int(int)> func;
    
    {
        int a = 3;
        func = [&a](int x){return a * x;};
    }
    
    return func(5);
}
```

�����func�����Q�Ƃ�dangle�ɂȂ��Ă��܂��̂Ŗ���`����B

Scheme�̏ꍇ

```Scheme
(define (func x) 0)

(define (hoge)
  (define a 3)
  (set! func (lambda (x) (* a x)))
  (define a 5)
  0)
```

�݂����Ȃ��Ƃ������

```
> (hoge)
0
> (func 10)
50
```

�ƂȂ�̂ŁA�����Q�Ƃ��n����Ă���񂾂Ǝv�����Adangle���Ƃ͂��Ԃ�Ȃ��񂾂Ǝv���B�����ƁB
����a��func���猩��Ύ��R�ϐ��Ɍ����邪�A�N���C�A���g���猩��ΉB������Ă���B

