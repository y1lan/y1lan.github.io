---
layout: post
title: Testing
tag: "note"
---

# Notation List

$R$: memory region

$\Phi$ functions: are added at join points as is standard

$\mu$ functions: used to identify potentially indirect uses at loads

$\chi$ functions: used to identify potentially indirect def at stores

$\theta$: may-use set, a set annotated to a statement of $\mu$ functions such that every $\mu(R)$ denotes a region in $R$ that maybe potentially read at the load

$\delta$: may-def set, a set annotated to a statement of $\chi$ functions such that every $R = \chi(R)$ indicates a region $R$ that may be potentially defined at the store

$[s]^{\theta}_{\delta}$: a statement with its may-use set $\theta$ and its may-def set $\delta$

$p$: pointer

$ps(p)$: a set of locations possibly pointed to by $p$


object $o \leftrightarrow$ location

$Local_f$: the set of all local locations accessed in $f$

$NonLocal_f$: the set of all local locations accessed in $f$

$\mathcal{U}$: the nonlocal memory locations in $f$ that may be indirectly read

$\mathcal{D}$: the nonlocal memory locations in $f$ that may be indirectly modified

$\vdash f: \mathcal{U}(\mathcal{D})$: denotes the above two definitions in $f$


$\vdash f \downarrow s: \mathcal{U}(\mathcal{D})$: inherited the above three definitions and limits the analysis boundry in just a statement $s$

$\mathscr{F}_k$: callsite

$N_{\mathscr{F}_k}$: the set of all nonlocal locations passed into this callsite $\mathscr{{F}_k}$

$Callees(\mathscr{F}_k)$: the set of callees possibly invoked at the callsite $\mathscr{F}_k$ 

$\mathbb{R}_f$: the set of memory regions

**Can you tell me the difference among "region", "location" and "object"?**

$R(v_1, \ldots, v_n)$: a region $R$ that represents variables $v_1, \ldots, v_n$

$\widehat{R_i}$: the def site of a memory region $R$ with version $i$

$\widehat{p_i}$: the def site of variable $p_i$, which forms a region by itself

$\widehat{CLOBAL}$: a global variable $g_i$ is abstracted as the unique $GLOBAL$ region

$\widehat{R_i} \xhookleftarrow[]{k} \widehat{R_j}$: values flow from the def site of region $R_j$ to the def site of $R_i$, where $k$ represents a callsite id for context-sensitive reachability analysis and is omitted if the flow is intraprocedural 

$PAR_g(q_j)$: the corresponding formal parameter of $q_j$ in SSA(version 0), where $g \in Callees(\mathscr{F}_k)$

$RET_g(p_i)$: the unique SSA variable return ed in function $g \in Callees(\mathscr{F}_k)$

$ENT_g = \{R \in \mathbb{R}_g | R \cap \mathcal{U} \neq \empty \}$: the set of nonlocal regions that may be read in $g$

$EXT_g = \{R \in \mathbb{R}_g | R \cup \mathcal{D} \neq \empty \}$: the set of nonlocal regions that mey be modified or the regions returned to a caller at the exit of $g$

$\texttt{vfpaths}(src, tgt)$: the set of all $value-flow paths$ from $src$ to $tgt$ in the SVFG

$\texttt{vfguard}(P)$: a Boolean formula that encodes the set of $control-flow paths$ that the underlying value reaches in the program, from $src$ to $tgt$, where $P \in \texttt{vfpaths}(src, tgt)$

$\textsf{FREED}(src) = \displaystyle \bigvee_{tgt \in \mathcal{S}_{src}} \bigvee_{P \in \texttt{vfpaths}(src, tgt)} \texttt{vfguard}(P)$: the sef of control-flow paths reaching a sink in $\mathcal{S}_{src}$ from $src$

$\texttt{vfedges}(P)$: set of all value-flow edges in $P$

$\texttt{cfguard}(\hat{p}, \hat{q})$: the set of control-flow paths in the program on which the def $\hat{p}$ reaches its use at the def $\hat{q}$ site, where $(\hat{p}, \hat{q}) \in \texttt{vfedges}(P)$






