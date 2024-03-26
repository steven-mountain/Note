<center>Latex快捷公式</center>

### 大括号分多行

```latex
l(e)=\left\{
\begin{aligned}
	1 &~~若 e\in E \\
	n &~~若 e\notin
\end{aligned}
\right.
```



### tikz 图上画方框

```latex
\begin{tikzpicture}
    % 引入图片
    \node[anchor=south west,inner sep=0] (image) at (0,0) {\includegraphics[width=0.9\textwidth]{Mycena_interrupta.jpg}};
 
    \begin{scope}[x={(image.south east)},y={(image.north west)}]
        % 建立相对坐标系
        \draw[help lines,xstep=.1,ystep=.1] (0,0) grid (1,1);
        \foreach \x in {0,1,...,9} { \node [anchor=north] at (\x/10,0) {0.\x}; }
        \foreach \y in {0,1,...,9} { \node [anchor=east] at (0,\y/10) {0.\y}; }
        % 作图命令
        \draw[red, ultra thick, rounded corners] (0.62,0.65) rectangle (0.78,0.75);
    \end{scope}
\end{tikzpicture}
```

