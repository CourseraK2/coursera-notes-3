## Communicating Results

## RPubs	
	
## Reproducible Research Checklist
	
	DOs and DON'Ts
	
	DO: Start With Good Science

	* Garbage in(put), garbage out(put)
	* Have a coherent, focused question that simplifies many problems
	* Working with good collaborators reinforces good practices
	* Something that's interesting to you will (hopefully) motivate good habits
	
	
	DON'T: Do Things By Hand

	* Editing spreadsheets of data to "clean it up"
		* Removing outliers
		* QA / QC, see [Data quality](http://en.wikipedia.org/wiki/Data_quality)
		* Validating
	* Editing tables or figures (e.g. rounding, formatting)
	* Downloading data from a web site (clicking links in a web browser)
	* Moving data around your computer; splitting / reformatting data files
	* "We're just going to do this once (so doing it by hand is acceptable)."
·
	Things done by hand need to be precisely documented (this is harder than it sounds)
	
	
	DON'T: Point And Click
	
	* In general, be careful with data analysis software that is highly _interactive_; ease of use can sometimes lead to non-reproducible analyses
	* Other interactive software, such as text editors, are usually ﬁne
	
	
	DO: Teach a Computer (by code)

	* If something needs to be done as part of your analysis / investigation, try to teach your computerto do it (even if you only need to do it once)
	* In order to give your computer instructions, you need to write down exactly what you mean to do and how it should be done
	* Teaching a computer almost guarantees reproducibilty
	
	
	DO: Use Some Version Control
	
	* Add changes in small chunks (don't just do one massive commit)
	
	
	DO: Keep Track of Your Software Environment
	
	* If you work on a complex project involving many tools / datasets, the software and computing environment can be critical for reproducing your analysis
	* Computer architecture: CPU (Intel, AMD, ARM), GPUs
	* Operating system: Windows, Mac OS, Linux / Unix, 32-bit or 64-bit
	* Software toolchain: Compilers, interpreters, command shell, programming languages (C, Perl, Python, etc.), database backends, data analysis software
	* Supporting software / infrastructure: Libraries, R packages, dependencies
	* External dependencies: Web sites, data repositories, remote databases, software repositories
	* Software version numbers: Ideally, for everything (if available)
	
	`sessionInfo()`
	
	
	DON'T: Save Output
	
	* Avoid saving data analysis output (tables, ﬁgures, summaries, processed data, etc.), except perhaps temporarily for efficiency purposes.
	* If a stray output file cannot be easily connected with the means by which it was created, then it is not reproducible.
	* Save the data + code that generated the output, rather than the output itself
	* Intermediate files are okay as long as there is clear documentation of how they were created
	
	
	DO: Set Your Seed
	
	* Random number generators generate pseudo-random numbers based on an initial seed (usually a number or set of numbers)
		* In R you can use the `set.seed()` function to set the seed and to specify the random number generator to use
	* Setting the seed allows for the stream of random numbers to be exactly reproducible
	* Whenever you generate random numbers for a non-trivial purpose, **always set the seed**

	
	DO: Think About the Entire Pipeline
	
	* Data analysis is a lengthy process; it is not just tables / figures / reports
	* Raw data → processed data → analysis → report
	* How you got the end is just as important as the end itself
	* The more of the data analysis pipeline you can make reproducible, the better for everyone
	
	
	Summary: Checklist
	
	* Are we doing good science?
	* Was any part of this analysis done by hand?
		* If so, are those parts precisely document?
		* Does the documentation match reality?
	* Have we taught a computer to do as much as possible (i.e. coded)?
	* Are we using a version control system?
	* Have we documented our software environment?
	* Have we saved any output that we cannot reconstruct from original data + code?
	* How far back in the analysis pipeline can we go before our results are no longer (automatically) reproducible?	
	
-----

## Evidence-based Data Analysis (part 1)
	
	Replication

	* Focuses on the validity of the scientiﬁc claim
	* "Is this claim true?"
	* The ultimate standard for strengthening scientiﬁc evidence
	* New investigators, data, analytical methods, laboratories, instruments, etc.
	* Particularly important in studies that can impact broad policy or regulatory decisions
	
	Reproducibility
	
	* Focuses on the validity of the data analysis
	* "Can we trust this analysis?"
	* Arguably (as can be proven) a minimum standard for any scientiﬁc study
	* New investigators, same data, same methods
	* Important when replication is impossible
	
	
	The current situations:
	
	* Some studies cannot be replicated: No time, No money, Unique/opportunistic
	* Technology is increasing data collection throughput; data are more complex and high-dimensional
	* Existing databases can be merged to become bigger databases
	* Computing power allows more sophisticated analyses, even on "small" data
	* For every ﬁeld "X" there is a "Computational X"
	
	The current situations lead to the results that:
	
	* Even basic analyses are difﬁcult to describe
	* Heavy computational requirements are thrust upon people without adequate training in statistics and computing
	* Errors are more easily introduced into long analysis pipelines
	* Knowledge transfer is inhibited
	* Results are difﬁcult to replicate or reproduce
	* Complicated analyses cannot be trusted
	
	So the analyses need to be Reproducible
	
## Evidence-based Data Analysis (part 2)

	What Problem Does Reproducibility Solve?

	What we get:
	
	* Transparency
	* Data Availability
	* Software / Methods Availability
	* Improved Transfer of Knowledge
	
	What we do NOT get:
	
	* Validity / Correctness of the analysis
	
	
	
	The problem of Reproducibility
	
	An analysis can be reproducible and still be wrong
	Does requiring reproducibility deter bad analysis? No.
	
	The premise of reproducible research is that with data/code available, people can check each other and the whole system is self-correcting.
	
	It only addresses the most “downstream” aspect of the research process – post-publication
	
## Evidence-based Data Analysis (part 3)
	
	这里实际是把发 paper 的过程比作了 SE，分四个阶段：
	
	Research conducted -> Paper submitted to journal -> Paper publication -> Post-publication review
	
	后三个阶段对应有三个审查制度，分别是：Editor's judgement, Peer review 和 Reproduction
	
	
	有趋势认为，Reproduction 可以在 Paper publication 期间就介入。但 lecturer 认为 this is unrealistic, speaking of experience, 因为这样给 Peer review 的负担太大了，而且会影响 publication 的进度
	
	
	lecuturer 介绍了一种在 journal of bio statistics 实施的改良，在 Peer review 阶段，给 paper 评分：
	
	C: has code
	D: has data
	C & D: has both
	R: reproducible (may be later)
	
## Evidence-based Data Analysis (part 4)

	Who Reproduces Research?
	
	* For reproducibility to be effective as a means to check validity, someone needs to do something
		* Re-run the analysis; check results match
		* Check the code for bugs/errors
		* Try alternate approaches; check sensitivity
	* The need for someone to do something is inherited from traditional notion of replication
	* Who is "someone" and what are their goals?
	·
	这里提到了 Reproducer 的态度问题：Reproducer 对 paper 的观点应该是中立的，不盲从也不抵制，因为他们的任务只是 reproduce

	
	lecturer 认为，reproduciblity 虽然 focus on "can we trust this analysis"，但并没有实际解决这个问题，所以要提出 Evidence-based Data Analysis 
	
	
	Evidence-based Data Analysis
	
	* Most data analyses involve stringing together many different tools and methods
	* Some methods may be standard for a given field, but others are often applied ad hoc
	* We should apply thoroughly studied (via statistical research), mutually agreed upon methods to analyze data whenever possible
	* There should be evidence to justify the application of a given method
		
## Evidence-based Data Analysis (part 5)	
	
	How to apply Evidence-based Data Analysis
	
	* Create analytic pipelines from evidence-based components – standardize it
	* A Deterministic Statistical Machine. see http://simplystatistics.org/2012/08/27/a-deterministic-statistical-machine/
	* Once an evidence-based analytic pipeline is established, we shouldn’t mess with it
	* Analysis with a “transparent box”
	* Reduce the "researcher degrees of freedom"
	* Analogous to a pre-speciﬁed clinical trial protocol
	
	
	感觉就是先建立流程模型，并且这个流程模型要经过验证。一开始可以拿少量的数据来试，模型建立后就拿全部的数据来实施。这里所谓的模型就是：step 1 用 xxx 统计方法，step 2 用 yyy 统计方法……这样的一个统计方法组合
	
	
	Where to Go From Here?
	
	* One DSM is not enough, we need many!
	* Different problems warrant different approaches and expertise
	* A curated library of machines providing state-of-the art analysis pipelines
	* A CRAN/CPAN/CTAN/… for data analysis
	* Or a “Cochrane Collaboration” for data analysis
	
	
	
	这个模型的建立对有两方面的意义：
	
	* 对 reviewer 而言：Gives reviewers an important tool without dramatically increasing the burden on them
	* 对 researcher 而言：Able to validate "upstream" aspects of scientiﬁc research
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	