---
title: "Generating flashcards with ChatGPT"
excerpt: >
  Let's explore how language models like ChatGPT can automate knowledge extraction to help humans learn.
date: 2023-03-08
last_modified_at: 2023-03-09
categories:
  - Projects
tags:
  - Learning
  - AI
share: false
---

Flashcards are a powerful tool for learning and memorizing information. They’re used by students, professionals, and lifelong learners to help retain various kinds of information. Recently I’ve found it’s possible to generate flashcards with ChatGPT.

After some experiments, I built this prompt:

```
I want you to create 5 Anki flashcards in english language from the source text I will specify below in english language.

Follow these rules to create the flashcards:

- Do not repeat words from questions in the answers if possible
- Difficulty of the created questions should be medium
- Keep questions as short as possible
- Questions should ask only one thing, do not formulate multilple questions in the same sentence
- Output the flashcards you create in JSON format, following this template: { "question": "...", "answer": "..." }. Do not output any plain text.

This is the source text: ""
```

This one is a parametrized prompt. You can customize:

- Amount of flashcards to generate
- Input/output language
- Questions difficulty
- Output format

Just insert your text inside the quotes in the prompt and send the message to ChatGPT. You will get the flashcards in JSON format. I've tested the prompt both with English and Italian text.

## Demo

To see my prompt in action, look at the following example. Its input text was extracted from _The History of Rome_ by Livy, freely available on [Project Gutenberg](https://www.gutenberg.org/ebooks/19725).

Input text to be quoted in the prompt:

```
Now first of all it is sufficiently established that, Troy having been
taken, the utmost severity was shown to all the other Trojans; but that
towards two, Æneas and Antenor, the Greeks forbore all the rights of
war, both in accordance with an ancient tie of hospitality, and because
they had ever been the advisers of peace, and of the restoration of
Helen--then that Antenor after various vicissitudes came into the
innermost bay of the Adriatic Sea, with a body of the Heneti, who having
been driven from Paphlagonia in consequence of a civil commotion, were
in quest both of a settlement and a leader, their king Pylæmenes having
been lost at Troy; and that the Heneti and Trojans, having expelled the
Euganei, who dwelt between the sea and the Alps, took possession of the
country; and the place where they first landed is called Troy; from
whence also the name of Trojan is given to the canton; but the nation in
general is called Veneti: that Æneas was driven from home by a similar
calamity, but the fates leading him to the founding of a greater empire,
he came first to Macedonia: that he sailed from thence to Sicily in
quest of a settlement: that from Sicily he made for the Laurentine
territory; this place also has the name of Troy. When the Trojans,
having disembarked there, were driving plunder from the lands,--as being
persons to whom, after their almost immeasurable wandering, nothing was
left but their arms and ships,--Latinus the king, and the Aborigines,
who then occupied those places, assembled in arms from the city and
country to repel the violence of the new-comers. On this point the
tradition is two-fold: some say, that Latinus, after being overcome in
battle, made first a peace, and then an alliance with Æneas: others,
that when the armies were drawn out in battle-array, before the signals
were sounded, Latinus advanced to the front of the troops and invited
the leader of the adventurers to a conference. That he then inquired who
they were, whence (they had come), or by what casualty they had left
their home, and in quest of what they had landed on the Laurentine
territory: after he heard that the host were Trojans, their chief Æneas,
the son of Anchises and Venus, and that, driven from their own country
and their homes, which had been destroyed by fire, they were seeking a
settlement and a place for building a town, struck with admiration of
the noble origin of the nation and of the hero, and their spirit, alike
prepared for peace or war, he confirmed the assurance of future
friendship by giving his right hand: that upon this a compact was struck
between the chiefs, and mutual greetings passed between the armies: that
Æneas was hospitably entertained by Latinus: that Latinus, in the
presence of his household gods, added a family league to the public one,
by giving Æneas his daughter in marriage. This event confirms the
Trojans in the hope of at length terminating their wanderings by a fixed
and permanent settlement. They build a town. Æneas calls it Lavinium,
after the name of his wife. In a short time, too, a son was the issue of
the new marriage, to whom his parents gave the name of Ascanius.

2. The Aborigines and Trojans were soon after attacked together in war.
Turnus, king of the Rutulians, to whom Lavinia had been affianced before
the coming of Æneas, enraged that a stranger had been preferred to
himself, made war on Æneas and Latinus together. Neither side came off
from that contest with cause for rejoicing. The Rutulians were
vanquished; the victorious Aborigines and Trojans lost their leader
Latinus. Upon this Turnus and the Rutulians, diffident of their
strength, have recourse to the flourishing state of the Etruscans, and
their king Mezentius; who holding his court at Cœre, at that time an
opulent town, being by no means pleased, even from the commencement, at
the founding of the new city, and then considering that the Trojan power
was increasing much more than was altogether consistent with the safety
of the neighbouring states, without reluctance joined his forces in
alliance with the Rutulians. Æneas, in order to conciliate the minds of
the Aborigines to meet the terror of so serious a war, called both
nations Latins, so that they might all be not only under the same laws,
but also the same name. Nor after that did the Aborigines yield to the
Trojans in zeal and fidelity towards their king Æneas; relying therefore
on this disposition of the two nations, who were now daily coalescing
more and more, although Etruria was so powerful, that it filled with the
fame of its prowess not only the land, but the sea also, through the
whole length of Italy, from the Alps to the Sicilian Strait, though he
might have repelled the war by means of fortifications, yet he led out
his forces to the field. Upon this a battle ensued successful to the
Latins, the last also of the mortal acts of Æneas. He was buried, by
whatever name human and divine laws require him to be called,[8] on the
banks of the river Numicius. They call him Jupiter Indiges.
```

ChatGPT output:

```
{"question": "Who did the Greeks spare after the fall of Troy?", "answer": "Æneas and Antenor."}
{"question": "Why did the Greeks spare Æneas and Antenor?", "answer": "Because of an ancient tie of hospitality and because they had ever been the advisers of peace and of the restoration of Helen."}
{"question": "Who was Antenor?", "answer": "One of two Trojans who were spared by the Greeks after the fall of Troy."}
{"question": "Who accompanied Antenor to the Adriatic Sea?", "answer": "A body of the Heneti."}
{"question": "Who founded Lavinium?", "answer": "Æneas."}
```

Not bad but there is still room for improvement. Some questions involve not-so-relevant parts in the input text (e.g. "Who accompanied Antenor to the Adriatic Sea?"). 
You might first summarize the input text so that ChatGPT generates questions on the most relevent parts only.

## Extra considerations

One might argue that manually creating flashcards helps you by itself to memorize the material. We know our brain can better remember something if we write it down somewhere. Still, this tool could be used by educators to quickly create flashcards. I think it can also generate quizzes as well as examination questions if you tweak the prompt just a little bit.

## Next steps

The prompt can be turned into a service that generates flashcards/quizzes from texts.

Some feature ideas:

- Text metadata extraction to categorize questions-answers pairs
- Exporting flashcards to [Anki](https://ankiweb.net/) format so they can be easily [imported](https://docs.ankiweb.net/importing.html#text-files)

But this is not a novel idea, as I guessed. I've found a couple of online service already doing this:

- [Monic AI](https://beta.monic.ai/)
- [Quizlet](https://quizlet.com/features/flashcards)

Still, my prompt can be used if you want a free hacky alternative. Hope you find it useful!