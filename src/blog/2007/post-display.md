title: Написание и отображение постов
date: 2007-09-10 18:47:12
tags: blog, javascript, markup, news
----


Сегодня прикрутил последнюю, похоже, часть в цепочке от написания поста до его отображения - [highlight.js][1]. И теперь пост проходит [markdown][2], [typogrify][3] и уже в браузере код расцвечивается хайлайтом. :)

Правда, сначала он показал мне баг в [smartypants][4] (который входит в состав [typogrify][3]) - он превращал кавычки в красивые ("") не только в тексте, но и в коде. А я хорошо помнил, что он уже два года не обновлялся, и просто чуял, что придётся мне сейчас рыться в его коде. :( Но всё же не поленился залезть в гугль, и увидеть что автор после двухлетнего перерыва обновил его и этот баг починен. :)

Вообще, из всего этого набора больше всего мне не нравится именно [markdown][2], потому что он однозначно рассчитан на английский язык - квадратных скобочек (для расставления ссылок) в русской раскладке нету. И ещё не нравится выделение кода четырьмя пробелами перед ним - неудобно в обычном текстедите их вставлять. :( Но альтернативы не вижу - Textile хуже имхо, а ReST - ничем не лучше маркдауна, у него обратные апострофы используются, которых точно также нету в русской раскладке. :(

[1]: http://softwaremaniacs.org/soft/highlight/ "И в тексте нету тонн спанов ;)"
[2]: http://www.freewisdom.org/projects/python-markdown/ "А есть ли что-то лучше?"
[3]: http://code.google.com/p/typogrify/ "Типографика - наше всё :)"
[4]: http://web.chad.org/projects/smartypants.py/ "Главная часть красивой типографики"
