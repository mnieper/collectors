# Collectors

A *collector* is two Scheme values - a procedure `collect` taking values and returning a new collector and a procedure `return` taking no values and returning values.

## Examples

```scheme
(define count-collector
  (lambda ()
    (let f ([count 0])
      (define collect
        (lambda (val)
          (f (+ count val))))
      (define return
        (lambda ()
          count))
      (values collect return))))

(define list-collect
  (lambda (ls collect return)
    (if (null? ls)
        (return)
        (let-values ([(collect return) (collect (car ls))])
          (list-collect (cdr ls) collect return)))))

(apply/mv list-collect '(1 2 3) (count-collector)) ; => 6

(define cdrop
  (lambda (n collect return)
    (let f ([n n])
      (values (if (zero? n)
                  collect
                  (lambda arg*
                    (f (- n 1))))
              return))))

(apply/mv list-collect '(1 2 3) (apply/mv cdrop 1 (count-collector))) ; => 5
```
