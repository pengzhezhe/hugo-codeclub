---
title: LaTeX 论文排版
date: 2022-08-22 17:11:04
tags:
- LaTeX
- 论文
---

前段时间收到了 APNOMS 的邮件，通知论文被录用了，最近根据审稿意见准备 Camera-Ready 稿件。在使用 LaTeX 对论文进行排版的过程中，也遇到了很多问题，这里记录一下在使用 LaTeX 排版中遇到的一些问题。

## 论文模板的使用

对于国际会议，一般根据出版社都会提供不同的模板，这里我投稿的会议使用的是 IEEE 提供的 [IEEE Conference Proceedings templates](http://www.ieee.org/conferences_events/conferences/publishing/templates.html)。下载对应的模板后，在本地使用 LaTeX 套件打开以 `.tex` 结尾的文件。

> 注意：使用 LaTeX 排版需要在本地下载 LaTeX 编译器，这里推荐两个最常用的 LaTeX 发行版：`MikTeX` 和 `TeXLive`，详细的安装过程可以在网上搜索相应的教程。如果你不想在本地下载 LaTeX 软件或者有多人协同的需求，可以考虑使用在线的 LaTeX 编辑器，例如 `Overleaf`。

## 作者多单位排版

默认的 IEEE Template 中每个作者只有一个单位，国内的高校可能要求单位不仅需要学院的信息还需要附上所属省（国家）级重点实验室的信息，默认的作者格式无法满足需求。这时就需要改动模板中作者信息的相关代码。

![默认的作者信息](https://s2.loli.net/2022/09/02/AsRDBQve27jzZ9V.png)

```latex
\DeclareRobustCommand*{\IEEEauthorrefmark}[1]{%
	\raisebox{0pt}[0pt][0pt]{\textsuperscript{\footnotesize #1}}%
}
\author{
	\IEEEauthorblockN{
		Author1\IEEEauthorrefmark{1, 2},
		Author2\IEEEauthorrefmark{1, 2},
		Author3\IEEEauthorrefmark{3} and
		Author4\IEEEauthorrefmark{1, 2}
	}
	\IEEEauthorblockA{
		\IEEEauthorrefmark{1}Name of the School, Wuhan 430081, China\\
		\IEEEauthorrefmark{2}Name of the Key Laboratory, Wuhan 430081, China\\
		\IEEEauthorrefmark{3}Name of the School, Guilin 541001, China\\
		Email:
        author1@mail.com
        author2@mail.com
        author3@mail.com
        author4@mail.com
	}
}
```

使用这段代码替换掉 IEEE Template 中的作者信息后，作者信息就变成了如下格式，每个作者可以有多个单位。

![修改后的作者信息](https://s2.loli.net/2022/09/02/UfNZHEmcq9XroW2.png)


## 图片排版

为保证论文图片的清晰度，推荐论文中的所有图片均使用 pdf 格式的矢量图（放大图片不会失真）。

在 LaTeX 中插入一张图片需要使用到 `graphicx` 宏包，然后使用 `figure` 环境插入图片即可实现图片的自动编号，例如

```latex
\begin{figure}[htbp]
\centering
    \includegraphics[width = \textwidth]{picture.pdf}
    \caption{Picture title.}
    \label{fig:1}
\end{figure}
```

通常由于篇幅的限制，我们一般需要将多张照片压缩成一张跨栏的大图。这时就需要用到子图环境，在 IEEE Template 中，官方推荐 `subfloat` 来插入子图，然后通过 `\includegraphics` 设置图片的宽度来对图片进行排版。大多数会议论文都是使用的双栏版本，插入多图时如果希望聚合后的大图占据双栏版面就需要在 `figure` 环境的后面加上 `*` 使这部分内容跨越多栏。同理，在插入表格时也可以在表格环境后面加上星号来创建跨栏表格。

在子图排版的过程中，我们需要设置每张图片的宽度来保证图片不会重叠和溢出。这里需要特别注意 LaTeX 中有关宽度的宏:

- \linewidth - 当前行的宽度
- \columnwidth - 当前分栏的宽度
- \textwidth - 整个页面版芯的宽度
- \paperwidth - 整个页面纸张的宽度

在单栏文本中，\columnwidth 和 \textwidth 保持一致；在多栏文本中 \textwidth = n * \columnwidth + (n - 1) * \columnsep（其中 n 是分栏数）。

在 minipage 环境中，除了 \paperwidth 之外，其它三个 \****width 都会根据 minipage 的宽度发生改变（因为虚拟出了一个小的纸张页面），然后在 minipage 环境结束的时候恢复原样。在 parbox 中，\textwidth 和 \columnwidth 不会改变，不过 \linewidth 会发生变化。

\linewidth 是相对最灵活的宽度值。在 list 环境里（包括 enumerate 和 itemize 等环境），在 \parbox 里，\linewidth 都会发生变化。

总的来说，当

- 需要在列表环境中使用表格、图片等宽度的时候，用 \linewidth
- 需要充满整个页面宽度的时候，用 \textwidth （比如 figure/table 等）
- 需要充满整个分栏的时候，用 \columnwidth （比如 figure/table/tabularx/tabu 等）

```latex
\begin{figure*}[!htbp]
	\centering
	\subfloat[]{
		\includegraphics[width=0.31\linewidth]{figure1.pdf}
		\label{flow entries number}
	}
	\hfill
	\subfloat[]{
		\includegraphics[width=0.31\linewidth]{figure2.pdf}
		\label{hit ratio}
	}
	\hfill
	\subfloat[]{
		\includegraphics[width=0.31\linewidth]{figure2.pdf}
		\label{workload}
	}
	\caption{Main title (a) Fig1 title; (b) Fig2 title; (c) Fig3 title}
\end{figure*}
```

## 参考文献

LaTeX主要有两种管理参考文献的方法，第一种方法是在 `.tex` 文档中嵌入参考文献，参考文献格式需符合特定的文献引用格式；另一种方法则是使用 BibTeX 进行文献管理，文件的拓展名为 `.bib`。其中，使用外部文件 BibTeX 管理文献更加便捷高效。

BibTeX 是 LaTeX 最为常用的一个文献管理工具，它通常以一个独立的文件出现，其拓展名为 `.bib`。它是伴随着 LaTeX 文档排版系统出现的，1985 年兰伯特博士与其合作者开发了这一工具。作为一种特殊的且独立于 LaTeX 文件 `.tex` 之外的数据库，它能大大简化 LaTeX 文档中的文献引用。实际上，BibTeX 文件中的文献都是用列表的形式罗列的，且不分先后顺序。通过使用引用命令如 `\cite{}` 即可在文档中自动生成特定格式的参考文献，其中，文档中的参考文献格式一般是提前设定好的。

使用 Bibtex 命令一个文献管理文件为sample.bib，将文献按照指定格式进行整理，插入参考文献并进行编译。

```bibtex
@article{einstein,
    author =       "Albert Einstein",
    title =        "{Zur Elektrodynamik bewegter K{\"o}rper}. ({German})
        [{On} the electrodynamics of moving bodies]",
    journal =      "Annalen der Physik",
    volume =       "322",
    number =       "10",
    pages =        "891--921",
    year =         "1905",
    DOI =          "http://dx.doi.org/10.1002/andp.19053221004"
}

@book{latexcompanion,
    author    = "Michel Goossens and Frank Mittelbach and Alexander Samarin",
    title     = "The \LaTeX\ Companion",
    year      = "1993",
    publisher = "Addison-Wesley",
    address   = "Reading, Massachusetts"
}

@misc{knuthwebsite,
    author    = "Donald Knuth",
    title     = "Knuth: Computers and Typesetting",
    url       = "http://www-cs-faculty.stanford.edu/\~{}uno/abcde.html"
}
```

在这三条文献中，einstein、latexcompanion、knuthwebsite 是文献的标签，在文档中，只需要在适当位置用引用命令如 \cite{} 便可以引用这些文献，例如，\cite{einstein}。


```latex
\documentclass[12pt]{article}

\begin{document}

Some examples for showing how to use \texttt{thebibliography} environment:
\begin{itemize}
    \item Book reference sample: The \LaTeX\ companion book \cite{latexcompanion}.
    \item Paper reference sample: On the electrodynamics of moving bodies \cite{einstein}.
    \item Open-source reference sample: Knuth: Computers and Typesetting \cite{knuthwebsite}.
\end{itemize}

\bibliographystyle{unsrt}
\bibliography{sample}

\end{document}
```

编译上述代码，得到文档如图所示。

![参考文献](https://s2.loli.net/2022/09/02/fKCUeB8pIgiO1Ry.png)

## 参考文章

- https://github.com/xinychen/latex-cookbook
- https://liam.page/2015/08/17/width-in-latex/
- https://www.overleaf.com/learn/latex/Bibliography_management_with_bibtex