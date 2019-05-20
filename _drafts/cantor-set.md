---
title: "Cantor Set and Regular Languages"
latex: true
---

I recently read a [blog post about the Cantor set](https://whatabouttheory.quora.com/The-Cantor-Set) written by a friend of mine, Jack Gindi. His post beautifully explains that the Cantor set is uncountably infinite while still having 0 Lebesgue measure. (I strongly urge you to go read Jack's post, firstly because it is great and secondly because it will provide the appropriate context to understand this post.)

Jack constructs the Cantor set \\(C\\) by taking the \\(\bigcap_{i=0}^\infty C_i\\) where \\(C_0 = [0, 1]\\) and \\(C_{i+1}\\) is obtained by removing the middle third from each of the intervals of \\(C_i\\). One of the claims made in the proof is that the set \\(E\\) of all endpoints of the intervals of all the \\(C_i\\) is countable and \\(E \subset C\\). I want to expound upon this statement via the use of regular languages.

To begin, let \\(\Sigma = \\{ 0, 1, 2 \\}\\), \\(L = \\{ w \in \Sigma^* ~ \vert ~ w = (0 + 2)^+ \\}\\). We will show that \\(L\\) is indeed regular by constructing a DFA \\(D\\) that accepts it.

<br><br>
<center><img alt="dfa.png" src="/img/dfa.png" height="256px"></center>
<br><br>

We will now endow \\(L\\) with meaning by interpreting its strings as follows. Let \\(T : L \mapsto (\Sigma \cup \\{.\\})^*\\) such that \\(T(w) = 0.w_3\\) represents the fractional part of the number \\(0.w\\) in the ternary numeral system (base 3). Under this interpretation, what kinds of numbers are represented by \\(L\\)?

$$\{ 0.0_3, 0.002_3, 0.02_3, 0.022_3, 0.2_3, 0.202_3, 0.22_3, 0.222_3, \dots \}$$

See the pattern? Good, because I don't see it either. Let's try grouping these numbers in the following way to make the pattern more apparent.

Let \\(A_k = \\{ w \in L ~ \vert ~ \vert w \vert = k \\}, \forall k \in \mathbb{N}\\). If we enumerate the first few \\(A_k\\), we get \\(A_0 = \emptyset\\), \\(A_1 = \\{ 0, 2 \\}\\), \\(A_2 = \\{ 00, 02, 20, 22 \\}\\), \\(A_3 = \\{ 000, 002, 020, 022, 200, 202, 220, 222 \\}\\), etc.

Let \\(\widehat{T}(A) = \\{ T(w) ~ \vert ~ w \in A \\}\\) be the extension of the mapping \\(T\\). Then, \\(\widehat{T}(A_1) = \\{ 0, \frac{2}{3} \\}\\), \\(\widehat{T}(A_2) = \\{ 0, \frac{2}{9}, \frac{2}{3}, \frac{8}{9} \\}\\), \\(\widehat{T}(A_3) = \\{ 0, \frac{2}{27}, \frac{2}{9}, \frac{8}{27}, \frac{2}{3}, \frac{20}{27}, \frac{8}{9}, \frac{26}{27} \\}\\).

As \\(k\\) tends towards infinity, \\(T(A_k)\\) will approximate the set of left endpoints of the intervals of the Cantor set! That is \\(E_L = \lim_{k \to \infty} T(A_k)\\) (where \\(E_L\\) and \\(E_R\\) respectively represent the left and right endpoints of the Cantor set). Since all endpoints are either left or right (and never both or neither), we know that \\(\vert E \vert  = \vert E_L \vert + \vert E_R \vert \\). Furthermore, since endpoints come in left-right pairs, we know that \\(\vert E_L \vert = \vert E_R \vert \\). Which means that \\(\vert E \vert = 2 \vert E_L \vert \\).

Now in order to show that \\(E\\) is countably infinite, we just need to show that \\(E_L\\) is countably infinite. Since there exists a bijection (TODO is this true? 0.0 could map to 0, 00, 000, ...) between \\(E_L\\) and \\(\bigcup_{k=0}^\infty A_k = L\\) by our construction of \\(E_L\\), it suffices to show that \\(L\\) is countably infinite. Using the easily proven fact that \\(\Sigma^*\\) is countably infinite, we know that \\(L\\) must be countable because \\(L \subseteq \Sigma^*\\). Clearly, \\(L\\) is infinite as the trim DFA \\(D\\) above that accepts it contains a loop.


Great! We've shown that the set of all endpoints of the Cantor set is countably infinite. But if that is the case, then how can the Cantor set be uncountable? The answer is that there are other points that are not endpoints that are in the Cantor set. We will now prove that the set of these points \\(U = C \setminus E\\) is uncountably infinite.

The points that are in \\(U\\) must have non-terminating ternary decimal expansions consisting of only 0's and 2's. One of these points is the number \\(0.020202 \dots_3 = \frac{2}{9} + \frac{2}{81} + \frac{2}{729} + \dots = 2 \times \sum_{i=1}^\infty (\frac{1}{9})^{i} = 2 \times \frac{\frac{1}{9}}{1 - \frac{1}{9}} = \frac{1}{4}\\). The corresponding strings for these points have infinite length. Since formal languages can only have finite-length words, such numbers are not represented in \\(L\\).

It appears that we are at a roadblock, since we have reached the limits of formal languages. Luckily, however, there is another concept that we can use that explicitly deals with languages of infinite length words. Introducing \\(\omega\\)-languages!  Note that all \\(\omega\\)-languages over \\(\Sigma\\) are subsets of \\(\Sigma^\omega\\), similar to how all formal languages over \\(\Sigma\\) are subsets of \\(\Sigma^*\\).

In particular, we are going to deal with a class of \\(\omega\\)-languages called \\(\omega\\)-regular languages. Definitionally, the language \\(A^\omega\\) is \\(\omega\\)-regular if \\(A\\) is a nonempty regular language such that \\(\epsilon \notin A\\).

We have shown above that \\(L\\) is a nonempty regular language. It is trivial to show \\(L\\) does not accept \\(\epsilon\\), as in our DFA \\(D\\) above \\(Q_0 \notin F\\). Therefore, \\(L^\omega\\) is an \\(\omega\\)-regular language.

Now we, only need to show that all \\(\omega\\)-languages are uncountably infinite.



Prove that if \\(A\\) is a regular language such that \\(\epsilon \notin A\\), then \\(A^\omega\\) is an \\(\omega\\)-regular language.

Prove the \\(\Sigma^\omega\\) is uncountable?


Note that all endpoints of the Cantor set have terminating decimal expansions in base 3.

Note that all numbers with only 0, 2 in ternary decimal expansions are in Cantor Set. And all numbers with a 1 in it are not.

\\(\omega\\)-languages are formed by taking the infinite concatenations of words in .
