from the [OpenCV forum](https://forum.opencv.org/t/eye-to-hand-calibration/5690)


![[handToeye.png]]

* gripper
* base
* target
* cam

This problem is also known as solving the $AX=XB$ equation:

* for  an eye-in-hand configuration:

$$
\begin{align}
	\prescript{b}{}T_g^{(1)}\prescript{g}{}T_c\prescript{c}{}T_t^{(1)}
	&= \prescript{b}{}T_g^{(2)}\prescript{g}{}T_c\prescript{c}{}T_t^{(2)} 
	\\
	(\prescript{b}{}T_g^{(2)})^{-1}\prescript{b}{}T_g^{(1)}\prescript{g}{}T_c
	&= \prescript{g}{}T_c\prescript{c}{}T_t^{(2)}(\prescript{c}{}T_t^{(1)})^{-1}\\
	\\
	A_iX&=XB_i
\end{align}
$$

* for an eye-to-hand configuration:

$$
\begin{align}
	\prescript{g}{}T_b^{(1)}\prescript{b}{}T_c\prescript{c}{}T_t^{(1)}
	&= \prescript{g}{}T_b^{(2)}\prescript{b}{}T_c\prescript{c}{}T_t^{(2)} 
	\\
	(\prescript{g}{}T_b^{(2)})^{-1}\prescript{g}{}T_b^{(1)}\prescript{b}{}T_c
	&= \prescript{b}{}T_c\prescript{c}{}T_t^{(2)}(\prescript{c}{}T_t^{(1)})^{-1}\\
	\\
	A_iX&=XB_i
\end{align}
$$

To get $\prescript{c}{}T_t$, use the [[SolvePnP]] and [[Aruco Mark]]