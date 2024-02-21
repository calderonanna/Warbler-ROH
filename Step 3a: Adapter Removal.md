---


---

<h1 id="adapter-removal-pipeline">Adapter Removal Pipeline</h1>
<p>Using the AdapterRemoval program, we will remove  the adapters at the ends of our reads. The adapters are added to our DNA so that polymerase has a scaffold to start transcription during the PCR step. After transcription, our reads also get the transcribed adapters. But, we don’t actually need the adapters and they need to be removed so we can preform our alignments later. To remove them, I employ a loop to go through each of our R1.fastq.gz and R2.fastq.gz files, and then apply the adapter removal tool. Below is a backbone of what needs to be included in the adapter removal command.</p>
<pre class=" language-bash"><code class="prism  language-bash">AdapterRemoval --file1 INPUT_R1.fastq --file2 INPUT_R2.fastq --file2 --collapse --trimns --minlength 20 --qualitybase 33 --basename filepath/OUTPUT_trim
</code></pre>
<p><strong>–collapse</strong>: collapses any regions where read 1 and read 2 overlap because the reads overlapped</p>
<pre><code>				ADAPTER1-------------------150bp-----------------&gt;
				ADAPTER1====================***********************==================ADAPTER2
									        &lt;----------------------150bp-------------ADAPTER2
</code></pre>
<p><strong>–trimns</strong>: trims any base that was called as ‘N’.<br>
<strong>–minlength 20</strong>: Filters out any reads less than 20bp<br>
<strong>–qualitybase 33</strong>: The input file is in phred 33 quality score<br>
<strong>–gzip</strong>: zip up the file<br>
<strong>–basename</strong>: specify location and name to store trimmed reads (aka the output file)</p>
<p>For additional flag information: <a href="https://adapterremoval.readthedocs.io/en/stable/manpage.html">https://adapterremoval.readthedocs.io/en/stable/manpage.html</a></p>
<h2 id="adapter-removal-script">Adapter Removal Script</h2>
<p>I employed a loop and parallel. The loop will echo the adapter removal tool command over all my samples and all those commands will get piped into parallel.</p>
<pre class=" language-bash"><code class="prism  language-bash"><span class="token function">nano</span> WarblerROH_trim.bash
------------------------------------------------NANO------------------------------------------------------
<span class="token comment">#!/bin/bash</span>
<span class="token comment">#PBS -l feature=rhel7 </span>
<span class="token comment">#PBS -l nodes=1:ppn=20</span>
<span class="token comment">#PBS -l walltime=336:00:00</span>
<span class="token comment">#PBS -l mem=1000gb</span>
<span class="token comment">#PBS -A zps5164_a_g_hc_default</span>

<span class="token keyword">set</span> -ue

<span class="token keyword">for</span>  i  <span class="token keyword">in</span> <span class="token variable"><span class="token variable">`</span><span class="token function">cat</span> ~/SzpiechLab/abc6435/Scripts/warbler_id_list.txt<span class="token variable">`</span></span><span class="token punctuation">;</span> <span class="token keyword">do</span> <span class="token keyword">echo</span> <span class="token string">" /storage/work/dut374/bin/adapterremoval-2.1.7/build/AdapterRemoval --file1 /storage/home/abc6435/SzpiechLab/abc6435/WarblerROH/<span class="token variable">${i}</span>/<span class="token variable">${i}</span>_R1.fastq.gz --file2 /storage/home/abc6435/SzpiechLab/abc6435/WarblerROH/<span class="token variable">${i}</span>/<span class="token variable">${i}</span>_R2.fastq.gz --collapse --trimns --minlength 20 --qualitybase 33 --gzip --basename /storage/home/abc6435/SzpiechLab/abc6435/WarblerROH/<span class="token variable">${i}</span>/<span class="token variable">${i}</span>_trimmed"</span><span class="token punctuation">;</span> <span class="token keyword">done</span> <span class="token operator">|</span> parallel -j20


/storage/work/dut374/bin/adapterremoval-2.1.7/build/AdapterRemoval --file1 split_reads.1.fq.gz --file2 split_reads.2.fq.gz --mate-separator / --collapse --trimns --minlength 20 --qualitybase 33 --gzip --basename /gpfs/group/dut374/default/johanna/ybch/ybch_trimmed

163_r1 --<span class="token operator">&gt;</span> @NB551436:220:HTH3YBGXH:1:11101:23579:1098 1:N:0:CAGATC
163_r2 --<span class="token operator">&gt;</span> @NB551436:220:HTH3YBGXH:1:11101:8669:1100 2:N:0:CAGATC

------------------------------------------END-OF-SCRIPT---------------------------------------------------
<span class="token operator">&lt;</span>CTRL O + Enter<span class="token operator">&gt;</span>
<span class="token operator">&lt;</span>CTRL X<span class="token operator">&gt;</span>
qsub WarblerROH_trim.bash
</code></pre>
<h2 id="quality-control">Quality Control</h2>
<pre class=" language-bash"><code class="prism  language-bash"><span class="token comment">#Check if the paired reads are there and you should also have 21 settings files for each</span>
<span class="token keyword">for</span> i <span class="token keyword">in</span> <span class="token variable"><span class="token variable">`</span><span class="token function">cat</span> ~/SzpiechLab/abc6435/Scripts/warbler_id_list.txt<span class="token variable">`</span></span><span class="token punctuation">;</span> <span class="token keyword">do</span> <span class="token function">ls</span> -l ~/SzpiechLab/abc6435/WarblerROH/<span class="token variable">${i}</span>/*pair*<span class="token punctuation">;</span> <span class="token keyword">done</span> <span class="token operator">|</span> <span class="token function">wc</span> -l

<span class="token keyword">for</span> i <span class="token keyword">in</span> <span class="token variable"><span class="token variable">`</span><span class="token function">cat</span> ~/SzpiechLab/abc6435/Scripts/warbler_id_list.txt<span class="token variable">`</span></span><span class="token punctuation">;</span> <span class="token keyword">do</span> <span class="token function">ls</span> -l ~/SzpiechLab/abc6435/WarblerROH/<span class="token variable">${i}</span>/*settings<span class="token punctuation">;</span> <span class="token keyword">done</span> <span class="token operator">|</span> <span class="token function">wc</span> -l
</code></pre>

