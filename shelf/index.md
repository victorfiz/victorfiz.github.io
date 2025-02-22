---
layout: none
title: Shelf
---

<html>
<head>
    <title>{{ page.title }}</title>
    <meta charset='UTF-8'>
    <meta content='width=device-width, initial-scale=1' name='viewport'/>
    <link rel="icon" type="image/png" sizes="48x48" href="/assets/images/favicon.png">

    <meta name='description' content='Victor Fizesan'>
    <meta name='keywords' content='{{ page.keywords }}'>
    <meta name='author' content='Victor Fizesan'>

    <link href='/assets/css/blog.css' rel='stylesheet'/>
    <link href='/assets/css/trac.css' rel='stylesheet'/>
    <link href='/assets/css/markdown.css' rel='stylesheet'/>
    <link href='/assets/css/shelf.css' rel='stylesheet'/>

    {% include mathjax.html %}
</head>
<body>
    {% include nav.html %}
    <div id='blog' class='wrap'>
        <div id='intro'>
            <div class='quote'>
                <p>This is a list of books, essays, ideas, and instruction manuals that I have found interesting. Highlighted in green are the ones that I wanted to highlight in green.</p>
            </div>
        </div>
        <div class='shelf-content'>
            {% capture my_markdown %}
* [John Stuart Mill – Autobiography](https://en.wikipedia.org/wiki/John_Stuart_Mill?useskin=vector)
* [The Beginning of Infinity – David Deutsch](https://en.wikipedia.org/wiki/The_Beginning_of_Infinity?useskin=vector){:.green}: the pursuit of knowledge through good explanations; a rational framework for optimism
* [HBS 1963](https://hbs1963.com/): 70 people looking back at their lives
* [Solomonoff Induction](https://en.wikipedia.org/wiki/Solomonoff%27s_theory_of_inductive_inference?useskin=vector)
* [Statistical Consequences of Fat Tails – N.N. Taleb](https://arxiv.org/abs/2001.10488){:.green}: we over-index on thin tailed distributions when predicting events; our world is [dominated by heavy tails](https://arxiv.org/pdf/1709.01450), which have unintuitive properties
* [The Structure of Scientific Revolutions](https://en.wikipedia.org/wiki/The_Structure_of_Scientific_Revolutions)
* [Classical Mechanics – Leonard Susskind](https://www.amazon.co.uk/Classical-Mechanics-Theoretical-Minimum/dp/0141976225)
* [Bureaucracy – Ludwig von Mises](https://cdn.mises.org/Bureaucracy_3.pdf)
* [Our Mathematical Universe – Max Tegmark](https://en.wikipedia.org/wiki/Our_Mathematical_Universe?useskin=vector)
* [Steve Jobs – Walter Isaacson](https://www.amazon.co.uk/Steve-Jobs-Exclusive-Walter-Isaacson/dp/034914043X/ref=pd_lpo_sccl_1/261-2611289-0292240?pd_rd_w=CDyvr&content-id=amzn1.sym.448aab69-6fff-42b9-9d7a-f6325052a6f6&pf_rd_p=448aab69-6fff-42b9-9d7a-f6325052a6f6&pf_rd_r=TQRGKCWNCB87AHDSGC8Z&pd_rd_wg=SlMyf&pd_rd_r=823ecf64-177b-4861-9237-dd538920870c&pd_rd_i=034914043X&psc=1)
* [Human Compatible – Stuart Russel](https://en.wikipedia.org/wiki/Human_Compatible?useskin=vector)
* [The Hitchhiker’s Guide to the Galaxy](https://en.wikipedia.org/wiki/The_Hitchhiker%27s_Guide_to_the_Galaxy?useskin=vector)
* [The History of Philosophy](https://www.amazon.co.uk/History-Philosophy-C-Grayling/dp/0241304547#:~:text=The%20History%20of%20Philosophy%20takes,and%20the%20Persian%2DArabic%20world.)
* [Crime and Punishment – Dostoevsky](https://en.wikipedia.org/wiki/Crime_and_Punishment?useskin=vector)
* [Information Theory, Inference, and Learning Algorithms – David MacKay](https://www.inference.org.uk/itprnn/book.pdf){:.green}
* [The Moon is a Harsh Mistress – Robert Heinlein](https://www.amazon.co.uk/Moon-Harsh-Mistress-Robert-Heinlein/dp/0312863551)
* [Memorylessness](https://en.wikipedia.org/wiki/Memorylessness?useskin=vector): all exponential distributions feel and are memoryless
* [Brave New World – Aldous Huxley](https://en.wikipedia.org/wiki/Brave_New_World?useskin=vector)
* [The Electric Kool-Aid Acid Test – Tom Wolfe](https://en.wikipedia.org/wiki/The_Electric_Kool-Aid_Acid_Test?useskin=vector)
* [The Dream Machine](https://press.stripe.com/the-dream-machine){:.green}: Licklider and the development of computers and the Internet
* [The Problems of Philosophy – Bertrand Russel](https://en.wikipedia.org/wiki/The_Problems_of_Philosophy?useskin=vector)
* [Meditations – Marcus Aurelius](https://en.wikipedia.org/wiki/Meditations?useskin=vector)
* [New Atlantis – Francis Bacon](https://en.wikipedia.org/wiki/New_Atlantis?useskin=vector)
* [The Little Book on Deep Learning](https://fleuret.org/public/lbdl.pdf)
* [Nabeel Quereshi – Principles](https://nabeelqu.co/principles)
* [Neuromorphic Computing: Semiconductors to Josephson Junctions](https://youtu.be/EwueqdgIvq4?si=mIKd4Z7dok1dqDM_)
* [The Gay Science: Nietzsche](https://en.wikipedia.org/wiki/The_Gay_Science?useskin=vector){:.green}: the necessity of living an authentic life in a world devoid of inherent meaning (how depressing)
* [Probability Theory: The Logic of Science](https://bayes.wustl.edu/etj/prob/book.pdf)
* [Maxwell’s Demon](https://en.wikipedia.org/wiki/Maxwell%27s_demon?useskin=vector)
* [Incrementalism](https://en.wikipedia.org/wiki/Incrementalism?useskin=vector)
* [St Petersburg  Paradox](https://en.wikipedia.org/wiki/St._Petersburg_paradox?useskin=vector)
* [Optimize for Volatility, not Average Capacity](https://jeremynixon.github.io/thinking/2015/06/22/optimize-for-volatility.html)
* [Smart Contracts (1994) Nick Szabo](https://www.fon.hum.uva.nl/rob/Courses/InformationInSpeech/CDROM/Literature/LOTwinterschool2006/szabo.best.vwh.net/smart.contracts.html)
* [How to Ask Smart Questions](http://www.catb.org/~esr/faqs/smart-questions.html)
* [Ira Glass on the Creative Process](https://www.goodreads.com/author/quotes/113989.Ira_Glass)
* [Building Products – Christina Cacioppo](https://web.archive.org/web/20170706174617/http://christinacacioppo.com/blog/build-products): the iterative process of improving projects.
* [Cosmic Natural Selection](https://en.wikipedia.org/wiki/Cosmological_natural_selection?useskin=vector)
* [Freeman Dyson Heresies](https://www.edge.org/conversation/freeman_dyson-heretical-thoughts-about-science-and-society){:.green}
* [Berkshire Annual Reports](https://www.berkshirehathaway.com/reports.html)
* [The Enlightened Economy - Joel Mokyr](https://www.amazon.com/Enlightened-Economy-Economic-History-1700-1850/dp/0300124554): An Economic History of Britain 1700-1850
* [Marc Andreesen Guide to Startups](https://fictivekin.github.io/pmarchive-jekyll/)
* [Gitlab Manual](https://about.gitlab.com/handbook/): managing culture remotely
* [David Hume and Adam Smith: The Infidel and the Professor](https://www.amazon.co.uk/Infidel-Professor-Friendship-Shaped-Thought/dp/0691177015)
* [The Royal Society & The Invention of Modern Science](https://www.amazon.co.uk/Royal-Society-Invention-Modern-Science/dp/1541673581)
* [The Innovator’s Dilemma](https://en.wikipedia.org/wiki/The_Innovator%27s_Dilemma): when incumbents fail to innovate
* [Complexity – Mitchell Waldrop](https://www.amazon.co.uk/Complexity-Emerging-Science-Order-Chaos/dp/0671872346): the emergence of the Santa Fe Institute
* [A Short History of Nearly Everything – Bill Bryson: ](https://www.amazon.co.uk/Short-History-Nearly-Everything-Bryson/dp/0552997048){:.green}: my childhood favourite
* [Fundraising – Ryan Breslow](https://www.amazon.co.uk/Fundraising-Ryan-Breslow/dp/B09CR7TFDT)
* [The Last Question – Isaac Asimov](https://users.ece.cmu.edu/~gamvrosi/thelastq.html){:.green}
* [Notes from the Underground – Dostoevsky](https://en.wikisource.org/wiki/White_Nights_and_Other_Stories/Notes_from_Underground/Part_1){:.green}
* [Philosophy in a Meaningless Life](https://library.oapen.org/bitstream/id/02cbd573-f1a1-41a2-9549-3757b58b4b7c/9781474247672.pdf): why existentialism should not influence how you live your life
* [Cognitive Load is what Matters](https://minds.md/zakirullin/cognitive): complexity and absctraction in software development
* [The Law – Frédéric Bastiat](https://en.wikipedia.org/wiki/The_Law_(Bastiat_book)?useskin=vector): the role of the government in protecting individual rights
* [Scaling People: Tactics for Management and Company Building](https://www.amazon.co.uk/Scaling-People-Tactics-Management-Building/dp/B0BXMFNHTY/ref=sr_1_1?crid=12XGNQMFZPYS9&keywords=Scaling+People%3A+Tactics+for+Management+and+Company+Building&qid=1703192798&sprefix=scaling+people+tactics+for+management+and+company+building%2Caps%2C92&sr=8-1)
* [High Growth Handbook – Elad Gil](https://www.amazon.com/High-Growth-Handbook-Elad-Gil/dp/1732265100/)
* [Napoleon: A life](https://www.goodreads.com/en/book/show/20821092)
* [Nuances of probability theory](https://tminka.github.io/papers/nuances.html?from=https://research.microsoft.com/~minka/papers/nuances.html&type=path)
* [Elon Musk – Walter Isaacson](https://www.amazon.co.uk/Elon-Musk-Walter-Isaacson/dp/1398527491)
* [Skin in the game – N.N. Taleb](https://en.wikipedia.org/wiki/Skin_in_the_Game_(book)?useskin=vector)
* [Atlas Shrugged – Ayn Rand](https://en.wikipedia.org/wiki/Atlas_Shrugged?useskin=vector){:.green}: rational self-interest and individualism
* [Immoderate Greatness: Why Civilizations Fail](https://www.amazon.com/Immoderate-Greatness-Why-Civilizations-Fail-ebook/dp/B00AVBHKEM/ref=tmm_kin_swatch_0?_encoding=UTF8&qid=&sr=): excessive complexity, resource depletion, and the inability to adapt
* [The Outsider – Albert Camus](https://en.wikipedia.org/wiki/The_Stranger_(Camus_novel)?useskin=vector)
* [A Brief History of Intelligence](https://www.goodreads.com/book/show/62050269-a-brief-history-of-intelligence)
* [The Metamorphosis of Prime Intellect](https://mogami.neocities.org/files/prime_intellect.pdf): superintelligence and its implications on humanity's purpose and existence
* [Malthusianism](https://en.wikipedia.org/w/index.php?title=Malthusianism&useskin=vector)
* [The Straussian Moment](https://gwern.net/doc/politics/2007-thiel.pdf): a critique of the West's dependence on liberal ideals (individualism and economic progress) in the face of human nature
* [No Man Is An Island](https://www.amazon.co.uk/No-Man-Island-Portrait-Singapores/dp/0044000286): A Study Of Singapore's Lee Kuan Yew
* [John Maynard Keynes: 1883-1946: Economist, Philosopher, Statesman](https://www.amazon.co.uk/John-Maynard-Keynes-1883-1946-Philosopher/dp/0143036157)
* [Lehninger: Principles of Biochemistry](https://www.amazon.co.uk/Lehninger-Principles-Biochemistry-Michael-Cox/dp/0716743396){:.green}
* [The Corrections – Jonathan Franzen](https://en.wikipedia.org/wiki/The_Corrections?useskin=vector): personal failures, moral dilemmas, and the changing socioeconomic landscape of late 20th-century America.
* [Principles – Ray Dalio](https://www.amazon.co.uk/Principles-Life-Work-Ray-Dalio/dp/1501124021)
* [Monetising Innovation – Madhavan Ramanujan](https://www.amazon.com/dp/B01F4DYY1I/ref=as_li_ss_tl?_encoding=UTF8&btkr=1&linkCode=sl1&tag=kidogo-20&linkId=47892f1007c721be262cce7f002cf10f)
* [Experimental Conversations – Timothy Odgen](https://www.amazon.co.uk/Experimental-Conversations-Perspectives-Randomized-Development/dp/0262035103): randomised controlled trials in development economics
* [The Beauty of Mathematics in Computer Science – Jun Wu](https://www.amazon.co.uk/Beauty-Mathematics-Computer-Science/dp/1138049603)
* [Grit: The Power of Passion and Perseverance](https://www.amazon.co.uk/Grit-Passion-Perseverance-Angela-Duckworth/dp/1501111108)
* [The Fountainhead – Ayn Rand](https://en.wikipedia.org/wiki/The_Fountainhead)
* [The Elements of Statistical Learning – Hastie](https://hastie.su.domains/ElemStatLearn/)
* [Invent and Wander (Jeff Bezos) – Walter Isaacson](https://www.amazon.co.uk/Invent-Wander-Collected-Writings-Introduction-ebook/dp/B08BCCT6MW)
* [Trillion Dollar Coach — Eric Schmidt](https://www.amazon.co.uk/Trillion-Dollar-Coach-Leadership-Handbook/dp/1473675960)
* [The Art of Statistics](https://www.amazon.co.uk/Art-Statistics-Learning-Pelican-Books/dp/0241398630)
* [The Hard Thing About Hard Things – Ben Horowitz](https://www.amazon.co.uk/Hard-Thing-About-Things-Building/dp/0062273205): managing crises, handling layoffs, and navigating complexities of scaling
* [No Rules (Netflix) – Reed Hastings](https://www.amazon.co.uk/No-Rules-Netflix-Culture-Reinvention/dp/1984877860): strategies for fostering innovation and creativity
* [The Phoenix Project](https://www.amazon.co.uk/Phoenix-Project-DevOps-Helping-Business/dp/0988262592): DevOps and project management
* [How to Win Friends & Influence People](https://www.amazon.co.uk/How-Win-Friends-Influence-People/dp/0671027034)
* [](https://en.wikipedia.org/w/index.php?title=List_of_superseded_scientific_theories&useskin=vector#Physics)
* [Delivering Happiness](https://www.amazon.co.uk/Delivering-Happiness-Profits-Passion-Purpose/dp/145550890X): company culture and customer service
* [The Ancient City – Imperium Press](https://www.amazon.co.uk/Ancient-City-Religion-Institutions-Greece/dp/0801823048): roles of religion, social hierarchy, and governance in urban development and the daily lives of ancient Greeks and Romans
* [The Machiavellians: Defenders of Freedom](https://www.amazon.co.uk/Machiavellians-Defenders-Freedom-James-Burnham/dp/1839013958/ref=sr_1_1?keywords=the+machiavellians&qid=1669055004&sprefix=the+machia%2Caps%2C124&sr=8-1): the importance of political power dynamics
* [Margin of Safety – Seth Klarman](https://en.wikipedia.org/wiki/Margin_of_Safety_(book)?useskin=vector)
* [Poor Charlie’s Almanac](https://www.stripe.press/poor-charlies-almanack)
* [Silicon Valley Product Group Blog](https://www.svpg.com/articles/)
* [The Intelligent Investor – Benjamin Graham](https://en.wikipedia.org/wiki/The_Intelligent_Investor?useskin=vector)
* [Visualisations and Design – Bret Victor](http://worrydream.com/SeeingSpaces/SeeingSpaces.jpg)
* [An Observation on Generalization](https://www.youtube.com/watch?v=AKMuA_TVz3A)
* [How to Pitch VCs – Vinod Khosla](https://www.khoslaventures.com/wp-content/uploads/Pitch-the-way-VCs-think_2019_FINAL.pdf)
* [Sales Advice (B2B) – David Hsu (Retool)](https://retool.com/startups/playbook/retool-founding-story-david-hsu)
* [Talking to customers – Christina Cacioppo](https://christinacacioppo.com/interviewing-users.html)
* [Chemical Structure and Reactivity](https://www.amazon.co.uk/Chemical-Structure-Reactivity-Integrated-Approach/dp/0199604134){:.green}
* [Andrej Karpathy – Deep Learning Videos](https://www.youtube.com/@AndrejKarpathy/videos  )
* [Cambridge Mathematics Tripos Notes – Dexter](https://dec41.user.srcf.net/notes/)
* [Two Paths to Intelligence – Hinton](https://youtu.be/rGgGOccMEiY?si=Piyec3W4pbXT3teA): biological vs. computational intelligence
* [Fetch random web 1.0 pages](https://wiby.me/surprise/)
* [Startup Advice – Keith Rabois](https://mogolshan.notion.site/Lessons-from-Keith-Rabois-2b867858346448998d23f51beee3470a)
* [Linear Algebra Done Right](https://www.amazon.co.uk/Linear-Algebra-Right-Undergraduate-Mathematics/dp/0387982582)
* [List of Eponymous Laws](https://en.wikipedia.org/wiki/List_of_eponymous_laws?useskin=vector)
* [Resources for quickly learning about AI](https://aman.ai/read/)
* [MIT Learning Challenge – How to Learn Fast](https://www.scotthyoung.com/blog/myprojects/mit-challenge-2/)
* [How to be a great CTO](https://github.com/kuchin/awesome-cto#technologies)
* [Bridgewater Insights Live](https://www.bridgewater.com/research-and-insights)
* [Brian Armstrong – HN Post: Looking for a co-founder!](https://news.ycombinator.com/item?id=3754664)
* [Unqualified Reservations](https://www.unqualified-reservations.org/2007/04/formalist-manifesto-originally-posted/){:.green}
* [How to hire – Suhail](https://twitter.com/suhail/status/1494150182392385536?s=21)
* [James Baldwin Speech – Cambridge Union](https://www.youtube.com/watch?v=5Tek9h3a5wQ)
* [Warren Buffet – Life, risk, principles, etc.](https://www.youtube.com/watch?v=M-vQGxTFfPU&list=FLXJuURIK0_r_mGlRWSPkgCw&index=105)
* [Jeff Bezos (2001)](https://www.youtube.com/watch?v=p7FgXSoqfnI){:.green}
* [The Future of Programming – Bret Victor](https://vimeo.com/worrydream)
* [Mathematical Methods for Physics and Engineering](https://www.amazon.co.uk/Mathematical-Methods-Physics-Engineering-Comprehensive/dp/0521679710){:.green}
* [Stephen Schwarzman – Family & Startups](https://youtu.be/aYwDs9LTN50?si=X4nC86kKTTWVKzw2&t=3590): life is a lonely road
* [Maximising User Happiness Unscalably – Patrick Collison](https://youtu.be/y_4emS6D4og?si=VL4zAFdvfFY3yBuo)
* [Evernote – How to build character into product](https://youtu.be/XM6NHNxS39E)
* [Unabomber Manifesto](https://www.washingtonpost.com/wp-srv/national/longterm/unabomber/manifesto.text.htm){:.green}
* [Bret Victor - Seeing Spaces: ](https://www.youtube.com/watch?v=klTjiXjqHrQ){:.green}: A living contrarian's approach to rethinking design
* [Marc Andreesen: History of Technology](https://www.youtube.com/watch?v=0jIqF2_PFFM)
* [Nat Friedman (Github CEO) Advice](https://nat.org/)
* [A Brief History of Intelligence](https://www.goodreads.com/book/show/62050269-a-brief-history-of-intelligence)
* [The Book of Why: The New Science of Cause and Effect – Judea Pearl](https://en.wikipedia.org/wiki/The_Book_of_Why?useskin=vector)
* [Metabolic Pathways](https://github.com/usnish/biochemical-pathways-poster/blob/master/prebuilt_hires.png)
* [Our World in Data](https://ourworldindata.org/charts)
* [Do Not Go Gentle into That Good Night – Dylan Thomas](https://www.poetryfoundation.org/poems/46569/do-not-go-gentle-into-that-good-night)
* [If – Rudyard Kipling](https://www.poetryfoundation.org/poems/46473/if---){:.green}
* [You can’t tell people anything](https://news.ycombinator.com/item?id=23617188): challenges of conveying new, complex ideas effectively
* [The Bitter Lesson – Rich Sutton](https://www.cs.utexas.edu/~eunsol/courses/data/bitter_lesson.pdf)
* [The Purpose of a System is What it Does](https://en.wikipedia.org/wiki/The_purpose_of_a_system_is_what_it_does?useskin=vector)
* [On the shortness of life – Seneca](https://ia804709.us.archive.org/9/items/SenecaOnTheShortnessOfLife/Seneca%20on%20the%20Shortness%20of%20Life.pdf)
* [The Usborne Science Encyclopedia](https://www.amazon.co.uk/Usborne-Science-Encyclopedia-Kirsteen-Rogers/dp/079453046X){:.green}
* [Most people want to change the world](https://bryce.vc/post/64889707700/most-people-wont)
* [State of ML 2017-Present – Jeff Dean](https://research.google/people/jeff/)
* [Recruiting - Vinod Khosla](https://www.khoslaventures.com/the-art-science-and-labor-of-recruiting/)
* [Sam Altman – the days are long but the decades are short](https://blog.samaltman.com/the-days-are-long-but-the-decades-are-short)
* [Zero to One – Peter Thiel](https://www.amazon.co.uk/Zero-One-Notes-Startups-Future/dp/0804139296){:.green}
* [Paul Graham Essays](https://www.paulgraham.com/articles.html){:.green}
            {% endcapture %}
            {{ my_markdown | markdownify }}
        </div>
    </div>
</body>
</html>
