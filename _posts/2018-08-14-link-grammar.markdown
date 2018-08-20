---
layout: post
title:  "Naive Mid-Sentence Processing"
date:   2018-08-14 17:00:00 -0400
---

![Demonstration of Naive Mid-Sentence Processing](/assets/mid-sentence.gif)

The first program I ever tried to create was an original process for making sense of a sentence of natural language as it was being spoken or written. With just a year of computer science classes under my belt and some experience tutoring the same material, this project was ambitious enough that I was probably doomed to fail. However, I would learn a lot in the process, not only about programming and working in a group but also about what kinds of projects I would like to work on going forward.

The idea for the project came from the [OpenCog Foundation](https://opencog.org/), who graciously offered to mentor me under the auspices of Google's Summer of Code 2015. As they pointed out, there had already existed algorithms for producing syntactic parses of complete sentences, such as the [Link Grammar Parser](https://www.abisource.com/projects/link-grammar/). What makes the Link Grammar Parser attractive to OpenCog, as Dr. Ben Goertzel once told me over Slack, is that the words have direct relations of dependency or hierarchy with one another, resulting in simpler parses (fewer nodes). In formal linguistic terminology, it is a syntax parser based on _dependency grammar_, rather than the _phrase structure grammar_ that most K-12 grammar textbooks and linguists such as Noam Chomsky primarily worked with, wherein groupings of words serving a particular purpose in the sentence may also count as nodes in the parse.

![An image showing the difference between dependency and phrase structure grammar parses of a sentence.](/assets/Thistreeisillustratingtherelation(PSG).png "By Tjo3ya - Own work, CC BY-SA 3.0, https://commons.wikimedia.org/w/index.php?curid=17527158")

To see it work, you can parse a complete sentence [here](http://www.link.cs.cmu.edu/link/submit-sentence-4.html). To understand what the relations in a given linkage mean, there is also a [reference dictionary](https://www.abisource.com/projects/link-grammar/dict/index.html).

In the hopes of developing an algorithm to predict syntactic parses mid-sentence on the basis of semantics, I spent the first half of my summer reading somewhat deeply into [Word Grammar](http://dickhudson.com/word-grammar/), a linguistic system describing on a theoretical level the relationships of many kinds of linguistic terms with each other (including words, spellings, pronunciations, syntactic links, and semantic concepts). I also looked into tweaking the primary algorithm to prioritize checking linkages within a limited portion of a sentence, such as a phrase.

What I didn't realize at the time was that the Link Grammar Parser is fast enough that it may have been first worth implementing a much simpler and more naive form of mid-sentence parsing -- rerunning the parser for each input action. So, as a tribute to my as-of-yet failure to improve upon the mid-sentence processing problem, I've implemented a small ruby script for the command line that repeatedly runs the link grammar parser every time a character is entered or deleted.

To run this program on your own Mac or Linux terminal, you'll need to install `ruby`, the command line tool `gem`, and the `linkparser` gem. On Debian/Ubuntu/derivatives, that should look like:
```
sudo apt-get install ruby gem

sudo gem install linkparser
```
To use sudo, you will need to enter your root-level password.

Then, you can use `git clone` from my [Github Repository]() or copy the code below into a file, and run it like so, assuming the file's name is _mid-sentence.rb_:

```
ruby mid-sentence.rb
```

Hit Escape to exit the program.

{% highlight ruby %}
require 'io/console'
require "linkparser"


dict = LinkParser::Dictionary.new(
  {max_null_count: 0}
)

shell_string = "linkparser> "
user_string  = ""
num_linkages_to_show = 4

puts "type Esc to exit program."


loop do
  print shell_string, user_string
  
  c = STDIN.getch

  if c == "\u007F"
    user_string = user_string[0...-1]
  elsif c == "\e"
    puts "\nExiting program..."
    exit
  else
    user_string = user_string + c
  end
  
  if !user_string.empty?    
    parse = dict.parse(user_string)    
    if parse.num_linkages_found.positive?
      print "\n" + "*" * 70 + "\n"      
      reversed = parse.linkages.reverse
      reversed[0...num_linkages_to_show].each do |link|
        print link.diagram(max_width: 200)
      end      
      print "#{parse.num_linkages_found} linkages found. "
      print "Best #{[num_linkages_to_show, parse.num_linkages_found].min} "
      print "shown in reverse, with the best matches on bottom."
    end    
  end
  
  print "\n"
end
{% endhighlight %}
