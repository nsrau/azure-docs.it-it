<properties 
	pageTitle="Materiale di riferimento per Application Analytics" 
	description="Espressioni regolari in Application Analytics, lo strumento di ricerca avanzato per Application Insights." 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/01/2016" 
	ms.author="awills"/>

# Materiale di riferimento

[Application Analytics](app-analytics.md) è un motore di ricerca avanzato per i dati di telemetria di [Application Insights](app-insights-overview.md). Queste pagine descrivono il linguaggio di query di Application Analytics (CSL).



[AZURE.INCLUDE [app-analytics-top-index](../../includes/app-analytics-top-index.md)]

## Espressioni regolari



[> Descrizione generale delle espressioni regolari](https://github.com/google/re2/wiki/Syntax).

Questa pagina descrive la sintassi delle espressioni regolari accettata da RE2. 
Viene inoltre descritta la sintassi accettata da PCRE, PERL e VIM.

||
|---|---
|Caratteri singoli: | 
|. |qualsiasi carattere, possibilmente con nuova riga (s=true) 
|[xyz] |classe di caratteri 
|[^xyz] |classe di caratteri negata 
|\\d |classe di caratteri Perl 
|\\D |classe di caratteri Perl negata 
|[[:alpha:]] |classe di caratteri ASCII 
|[[:^alpha:]] |classe di caratteri ASCII negata 
|\\pN |classe di caratteri Unicode (nome di una sola lettera) 
|\\p{Greek} |classe di caratteri Unicode 
|\\PN |classe di caratteri Unicode negata (nome di una sola lettera) 
|\\P{Greek} |classe di caratteri Unicode negata 
|Composti: | 
|xy |x seguito da y 
|x&#124;y |x o y (di preferenza x) 
| 
|Ripetizioni: | 
| |zero o più x, preferire più 
|x+ |una o più x, preferire più 
|x? |zero o una x, preferire una 
|x{n,m} |n o n+1 o ... o m x, preferire più 
|x{n,} |n o più x, preferire più 
|x{n} |esattamente n x 
|x*? |zero o più x, preferire meno 
|x+? |una o più x, preferire meno 
|x?? |zero o una x, preferire zero 
|x{n,m}? |n o n+1 o ... o m x, preferire meno 
|x{n,}? |n o più x, preferire meno 
|x{n}? |esattamente n x 
|x{} |(== x*) (NON SUPPORTATA) VIM 
|x{-} |(== x*?) (NON SUPPORTATA) VIM 
|x{-n} |(== x{n}?) (NON SUPPORTATA) VIM 
|x= |(== x?) (NON SUPPORTATA) VIM 
|Restrizione di implementazione: il conteggio forma x{n,m}, x{n,} e x{n} | 
|rifiutare le forme che creano un conteggio di ripetizioni minimo o massimo maggiore di 1000. | 
|Le ripetizioni illimitate non sono soggette a questa restrizione. | 
|Ripetizione possessive: | 
|x*+ |zero o più x, possessiva (NON SUPPORTATA) 
|x++ |una o più x, possessiva (NON SUPPORTATA) 
|x?+ |zero o più x, possessiva (NON SUPPORTATA) 
|x{n,m}+ |n o ... o m x, possessiva (NON SUPPORTATA) 
|x{n,}+ |n o più x, possessiva (NON SUPPORTATA) 
|x{n}+ |esattamente n x, possessiva (NON SUPPORTATA) 
|Raggruppamento: | 
|(re) |gruppo di acquisizione numerato (sottocorrispondenza) 
|(?P<name>re) |gruppo di acquisizione denominato e numerato (sottocorrispondenza) 
|(?<name>re) |gruppo di acquisizione denominato e numerato (sottocorrispondenza) (NON SUPPORTATO) 
|(?'name're) |gruppo di acquisizione denominato e numerato (sottocorrispondenza) (NON SUPPORTATO) 
|(?:re) |gruppo di non acquisizione 
|(?flags) |impostare flag all'interno del gruppo corrente; non di acquisizione 
|(?flags:re) |impostare flag durante re; non di acquisizione 
|(?#text) |commento (NON SUPPORTATO) 
|(?&#124;x&#124;y&#124;z) |reimpostazione numerazione ramo (NON SUPPORTATO) 
|(?>re) |corrispondenza possessiva di re (NON SUPPORTATO) 
|re@> |corrispondenza possessiva di re (NON SUPPORTATO) VIM 
|%(re) |gruppo di non acquisizione (NON SUPPORTATO) VIM 
|Flag: | 
|i |senza distinzione tra maiuscole e minuscole (valore predefinito false) 
|m |modalità multiriga: ^ e $ corrispondono a riga inizio/fine in aggiunta a testo inizio/fine (valore predefinito false) 
|s |consentire . corrispondenza \\n (valore predefinito false) 
|U |ungreedy: scambiare significato di x* e x*?, x+ e x+?, ecc. (valore predefinito false) 
|La sintassi del flag è xyz (set) o -xyz (clear) o xy-z (set xy, clear z). | 
|Stringhe vuote: | 
|^ |all'inizio del testo o della riga (m=true) 
|$ |alla fine del testo (come \\z non \\Z) o della riga (m=true) 
|\\A |all'inizio del testo 
|\\b |su confine di parola ASCII (\\w su un lato \\W, \\A o \\z sull'altro lato) 
|\\B |non su confine di parola ASCII 
|\\G |all'inizio del sottotesto ricercato (NON SUPPORTATO) PCRE 
|\\G |alla fine dell'ultima corrispondenza (NON SUPPORTATO) PERL 
|\\Z |alla fine del testo o prima della nuova riga alla fine del testo (NON SUPPORTATO) 
|\\z |alla fine del testo 
|(?=re) |prima della corrispondenza testo re (NON SUPPORTATO) 
|(?!re) |prima della non corrispondenza testo re (NON SUPPORTATO) 
|(?<=re) |dopo la corrispondenza testo re (NON SUPPORTATO) 
|(?<!re) |dopo la non corrispondenza testo re (NON SUPPORTATO) 
|re& |prima della corrispondenza testo re (NON SUPPORTATO) VIM 
|re@= |prima della corrispondenza testo re (NON SUPPORTATO) VIM 
|re@! |prima della non corrispondenza testo re (NON SUPPORTATO) VIM 
|re@<= |dopo la corrispondenza testo re (NON SUPPORTATO) VIM 
|re@<! |dopo la non corrispondenza testo re (NON SUPPORTATO) VIM 
|\\zs |imposta l'inizio della corrispondenza (= \\K) (NON SUPPORTATO) VIM 
|\\ze |imposta la fine della corrispondenza (NON SUPPORTATO) VIM 
|\\%^ |inizio del file (NON SUPPORTATO) VIM 
|\\%$ |fine del file (NON SUPPORTATO) VIM 
|\\%V |su schermo (NON SUPPORTATO) VIM 
|\\%# |cursor position (NON SUPPORTATO) VIM 
|\\%'m |mark m position (NON SUPPORTATO) VIM 
|\\%23l |in line 23 (NON SUPPORTATO) VIM 
|\\%23c |in colonna 23 (NON SUPPORTATO) VIM 
|\\%23v |in colonna virtuale 23 (NON SUPPORTATO) VIM 
|Sequenze Escape: | 
|\\a |campana (== \\007) 
|\\f |form feed (== \\014) 
|\\t |scheda orizzontale (== \\011) 
|\\n |nuova riga (== \\012) 
|\\r |ritorno a capo (== \\015) 
|\\v |carattere scheda verticale (== \\013) 
|\* |letterale \*, per tutti i caratteri di punteggiatura * 
|\\123 |codice carattere ottale (fino a tre cifre) 
|\\x7F |codice carattere esadecimale (esattamente due cifre) 
|\\x{10FFFF} |codice carattere esadecimale 
|\\C |corrispondenza byte singolo anche in modalità UTF-8 
|\\Q...\\E |testo letterale ... anche con ... punteggiatura 
|\\1 |backreference (NON SUPPORTATO) 
|\\b |Backspace (NON SUPPORTATO) (usare \\010) 
|\\cK |caratere ctrl ^K (NON SUPPORTATO) (usare \\001 ecc) 
|\\e |escape (NON SUPPORTATO) (usare \\033) 
|\\g1 |backreference (NON SUPPORTATO) 
|\\g{1} |backreference (NON SUPPORTATO) 
|\\g{+1} |backreference (NON SUPPORTATO) 
|\\g{-1} |backreference (NON SUPPORTATO) 
|\\g{name} |backreference denominato (NON SUPPORTATO) 
|\\g<name> |chiamata subroutine (NON SUPPORTATO) 
|\\g'name' |chiamata subroutine (NON SUPPORTATO) 
|\\k<name> |backreference denominato (NON SUPPORTATO) 
|\\k'name' |backreference denominato (NON SUPPORTATO) 
|\\lX |X minuscola (NON SUPPORTATO) 
|\\ux |x maiuscola(NON SUPPORTATO) 
|\\L...\\E |testo minuscolo ... (NON SUPPORTATO) 
|\\K |reimpostare inizio di $0 (NON SUPPORTATO) 
|\\N{name} |carattere Unicode denominato (NON SUPPORTATO) 
|\\R |interruzione riga (NON SUPPORTATO) 
|\\U...\\E |testo maiuscolo ... (NON SUPPORTATO) 
|\\X |sequenza Unicode estesa (NON SUPPORTATO) 
|\\%d123 |carattere decimale 123 (NON SUPPORTATO) VIM 
|\\%xFF |FF carattere esadecimale (NON SUPPORTATO) VIM 
|\\%o123 |carattere ottale 123 (NON SUPPORTATO) VIM 
|\\%u1234 |carattere Unicode 0x1234 (NON SUPPORTATO) VIM 
|\\%U12345678 |carattere Unicode 0x12345678 (NON SUPPORTATO) VIM 
|Elementi della classe di caratteri: | 
|x |carattere singolo 
|A-Z |intervallo di caratteri (incluso) 
|\\d |classe di caratteri Perl 
|[:foo:] |classe di caratteri ASCII foo 
|\\p{Foo} |classe di caratteri Unicode Foo 
|\\pF |F classe di caratteri Unicode F (nome di una sola lettera) 
|Classi di caratteri denominate come elementi di classe di caratteri: | 
|[\\d] |cifre (== \\d) 
|[^\\d] |non cifre (== \\D) 
|[\\D] |non cifre (== \\D) 
|[^\\D] |non non cifre (== \\d) 
|[[:name:]] |classe ASCII denominata all'interno di classe di caratteri (== [:name:]) 
|[^[:name:]] |classe ASCII denominata all'interno classe di caratteri negata (== [:^name:]) 
|[\\p{Name}] |proprietà Unicode denominata all'interno di classe di caratteri (== \\p{Name}) 
|[^\\p{Name}] |proprietà Unicode denominata all'interno di classe di caratteri negata (== \\P{Name}) 
|Classi di caratteri Perl (tute solo ASCII): | 
|\\d |cifre (== [0-9]) 
|\\D |non cifre (== [^0-9]) 
|\\s |spazio vuoto (== [\\t\\n\\f\\r ]) 
|\\S |non spazio vuoto (== [^\\t\\n\\f\\r ]) 
|\\w |caratteri alfanumerici (== [0-9A-Za-z\_]) 
|\\W |caratteri non alfanumerici (== [^0-9A-Za-z\_]) 
|\\h |spazio orizzontale (NON SUPPORTATO) 
|\\H |spazio non orizzontale (NON SUPPORTATO) 
|\\v |spazio verticale (NON SUPPORTATO) 
|\\V |spazio non verticale (NON SUPPORTATO) 
|classi di caratteri ASCII: | 
|[[:alnum:]] |alfanumerico (== [0-9A-Za-z]) 
|[[:alpha:]] |alfabetico (== [A-Za-z]) 
|[[:ascii:]] |ASCII (== [\\x00-\\x7F]) 
|[[:blank:]] |vuoto (== [\\t ]) 
|[[:cntrl:]] |control (== [\\x00-\\x1F\\x7F]) 
|[[:digit:]] |cifre (== [0-9]) 
|[[:graph:]] |grafico (== [!-~] == [A-Za-z0-9!"#$%&'()*+,-./:;<=>?@[\\]^\_`{&#124;}~]) 
|[[:lower:]] |lower case (== [a-z]) 
|[[:print:]] |printable (== [ -~] == [ [:graph:]]) 
|[[:punct:]] |punctuation (== [!-/:-@[-`{-~]) 
|[[:space:]] |spazio vuoto (== [\\t\\n\\v\\f\\r ]) 
|[[:upper:]] |maiuscole (== [A-Z]) 
|[[:word:]] |caratteri alfanumerici (== [0-9A-Za-z\_]) 
|[[:xdigit:]] |cifra esadecimale (== [0-9A-Fa-f]) 
|Nomi di classi di caratteri Unicode--categoria generale: | 
|C |altro 
|Cc |control 
|Cf |formato 
|Cn |punti di codice non assegnati (NON SUPPORTATO) 
|Co |uso privato 
|Cs |surrogato 
|L |lettera 
|LC |lettera con maiuscole/minuscole (NON SUPPORTATO) 
|L& |lettera con maiuscole/minuscole (NON SUPPORTATO) 
|Ll |lettera minuscola 
|Lm |lettera modificatore 
|Lo |altra lettera 
|Lt |lettera iniziale maiuscola 
|Lu |lettera maiuscola 
|M |contrassegno 
|Mc |contrassegno spaziatura 
|Me |contrassegno di inclusione 
|Mn |contrassegno non spaziatura 
|N |numero 
|Nd |numero decimale 
|Nl |numero lettera 
|No |altro numero 
|P |punteggiatura 
|Pc |punteggiatura connettore 
|Pd |punteggiatura trattino 
|Pe |punteggiatura chiusura 
|Pf |punteggiatura finale 
|Pi |punteggiatura iniziale 
|Po |altra punteggiatura 
|Ps |punteggiatura aperta 
|S |simbolo 
|Sc |simbolo di valuta 
|Sk |simbolo modificatore 
|Sm |simbolo matematico 
|So |altro simbolo 
|Z |separatore 
|Zl |separatore di riga 
|Zp |separatore di paragrafo 
|Zs |separatore di spazio 
|Nomi di classi di caratteri Unicode--script: | 
|Arabic |Arabo 
|Armenian |Armeno 
|Balinese |Balinese 
|Bamum |Bamum 
|Batak |Batak 
|Bengali |Bengali 
|Bopomofo |Bopomofo 
|Brahmi |Brahmi 
|Braille |Braille 
|Buginese |Buginese 
|Buhid |Buhid 
|Canadian\_Aboriginal |Canadese autoctono 
|Carian |Cario 
|Chakma |Chakma 
|Cham |Cham 
|Cherokee |Cherokee 
|Common |caratteri non specifici di uno script 
|Coptic |Copto 
|Cuneiform |Cuneiforme 
|Cypriot |Cipriota 
|Cyrillic |Cirillico 
|Deseret |Deseret 
|Devanagari |Devanagari 
|Egyptian\_Hieroglyphs |Geroglifici egiziani 
|Ethiopic |Etiopico 
|Georgian |Georgiano 
|Glagolitic |Glagolitico 
|Gothic |Gotico 
|Greek |Greco 
|Gujarati |Gujarati 
|Gurmukhi |Gurmukhi 
|Han |Han 
|Hangul |Hangul 
|Hanunoo |Hanunoo 
|Hebrew |Ebraico 
|Hiragana |Hiragana 
|Imperial\_Aramaic |Aramaico imperiale 
|Inherited |script ereditato da carattere precedente 
|Inscriptional\_Pahlavi |Pahlavi (Inscriptional) 
|Inscriptional\_Parthian |Partico (iscrizioni) 
|Javanese |Giavanese 
|Kaithi |Kaithi 
|Kannada |Kannada 
|Katakana |Katakana 
|Kayah\_Li |Kayah Li 
|Kharoshthi |Kharoshthi 
|Khmer |Khmer 
|Lao |Lao 
|Latin |Latino 
|Lepcha |Lepcha 
|Limbu |Limbu 
|Linear\_B |Lineare B 
|Lycian |Licio 
|Lydian |Lidio 
|Malayalam |Malayalam 
|Mandaic |Mandaico 
|Meetei\_Mayek |Meetei Mayek 
|Meroitic\_Cursive |Meroitico corsivo 
|Meroitic\_Hieroglyphs |Geroglifici meroitici 
|Miao |Miao 
|Mongolian |Mongolo 
|Myanmar |Myanmar 
|New\_Tai\_Lue |Tai Lue semplificato 
|Nko |Nko 
|Ogham |Ogham 
|Ol\_Chiki |Ol Chiki 
|Old\_Italic |Italico 
|Old\_Persian |Persiano antico 
|Old\_South\_Arabian |Arabo meridionale antico 
|Old\_Turkic |Turkic antico 
|Oriya |Oriya 
|Osmanya |Osmanya 
|Phags\_Pa |'Phags Pa 
|Phoenician |Fenicio 
|Rejang |Rejang 
|Runic |Runico 
|Saurashtra |Saurashtra 
|Sharada |Sharada 
|Shavian |Shavian 
|Sinhala |Sinhala 
|Sora\_Sompeng |Sora Sompeng 
|Sundanese |Sundanese 
|Syloti\_Nagri |Syloti Nagri 
|Syriac |Siriaco 
|Tagalog |Tagalog 
|Tagbanwa |Tagbanwa 
|Tai\_Le |Tai Le 
|Tai\_Tham |Tai Tham 
|Tai\_Viet |Tai Viet 
|Takri |Takri 
|Tamil |Tamil 
|Telugu |Telugu 
|Thaana |Thaana 
|Thai |Thai 
|Tibetan |Tibetano 
|Tifinagh |Tifinagh 
|Ugaritic |Ugaritico 
|Vai |Vai 
|Yi |Yi 
|Classi di caratteri Vim: | 
|\\i |carattere identificatore (NON SUPPORTATO) VIM 
|\\I |\\i cifre eccezione (NON SUPPORTATO) VIM 
|\\k |carattere parola chiave (NON SUPPORTATO) VIM 
|\\K |\\k cifre eccezione (NON SUPPORTATO) VIM 
|\\f |carattere nome file (NON SUPPORTATO) VIM 
|\\F |\\f cifre eccezione (NON SUPPORTATO) VIM 
|\\p |carattere stampabile (NON SUPPORTATO) VIM 
|\\P |\\p cifre eccezione (NON SUPPORTATO) VIM 
|\\s |carattere spazio vuoto (== [ \\t]) (NON SUPPORTATO) VIM 
|\\S |carattere spazio non vuoto (== [^ \\t]) (NON SUPPORTATO) VIM 
|\\d |cifre (== [0-9]) VIM 
|\\D |non \\d VIM 
|\\x |cifre esadecimali (== [0-9A-Fa-f]) (NON SUPPORTATO) VIM 
|\\X |non \\x (NON SUPPORTATO) VIM 
|\\o |cifre ottali (== [0-7]) (NON SUPPORTATO) VIM 
|\\O |non \\o (NON SUPPORTATO) VIM 
|\\w |carattere alfanumerico VIM 
|\\W |non \\w VIM 
|\\h |carattere inizio parola (NON SUPPORTATO) VIM 
|\\H |non \\h (NON SUPPORTATO) VIM 
|\\a |alfabetico (NON SUPPORTATO) VIM 
|\\A |non \\a (NON SUPPORTATO) VIM 
|\\l |minuscolo (NON SUPPORTATO) VIM 
|\\L |non minuscolo (NON SUPPORTATO) VIM 
|\\u |maiuscolo (NON SUPPORTATO) VIM 
|\\U |non maiuscolo (NON SUPPORTATO) VIM 
|\_x |\\x più nuova riga, per tutte le x (NON SUPPORTATO) VIM 
|Flag Vim: | 
|\\c |ignorare maiuscole e minuscole (NON SUPPORTATO) VIM 
|\\C |distinguere maiuscole e minuscole (NON SUPPORTATO) VIM 
|\\m |magic (NON SUPPORTATO) VIM 
|\\M |nomagic (NON SUPPORTATO) VIM 
|\\v |verymagic (NON SUPPORTATO) VIM 
|\\V |verynomagic (NON SUPPORTATO) VIM 
|\\Z |ignorare le differenze nei caratteri di unione Unicode (NON SUPPORTATO) VIM 
|Magic: | 
|(?{code}) |codice Perl arbitrario (NON SUPPORTATO) PERL 
|(??{code}) |codice Perl arbitrario posposto (NON SUPPORTATO) PERL 
|(?n) |chiamata ricorsiva a gruppo di acquisizione regexp n (NON SUPPORTATO) 
|(?+n) |chiamata ricorsiva a gruppo relativo +n (NON SUPPORTATO) 
|(?-n) |chiamata ricorsiva a gruppo relativo -n (NON SUPPORTATO) 
|(?C) |callout PCRE (NON SUPPORTATO) PCRE 
|(?R) |chiamata ricorsiva a intero regexp (== (?0)) (NON SUPPORTATO) 
|(?&name) |chiamata ricorsiva a gruppo denominato (NON SUPPORTATO) 
|(?P=name) |backreference denominato (NON SUPPORTATO) 
|(?P>name) |chiamata ricorsiva a gruppo denominato (NON SUPPORTATO) 
|(?(cond)true&#124;false) |diramazione condizionale (NON SUPPORTATO) 
|(?(cond)true) |diramazione condizionale (NON SUPPORTATO) 
|(*ACCEPT) |modificare regexp rendendoli più simili a Prolog (NON SUPPORTATO) 
|(*COMMIT) |(NON SUPPORTATO) 
|(*F) |(NON SUPPORTATO) 
|(*FAIL) |(NON SUPPORTATO) 
|(*MARK) |(NON SUPPORTATO) 
|(*PRUNE) |(NON SUPPORTATO)
|(*SKIP) |(NON SUPPORTATO)
|(*THEN) |(NON SUPPORTATO) 
|(*ANY) |impostare convenzione nuova riga (NON SUPPORTATO) 
|(*ANYCRLF) |(NON SUPPORTATO) 
|(*CR) |(NON SUPPORTATO) 
|(*CRLF) |(NON SUPPORTATO) 
|(*LF) |(NON SUPPORTATO)
|(*BSR_ANYCRLF) |set \R convention (NON SUPPORTATO) PCRE 
|(*BSR_UNICODE) |(NON SUPPORTATO) PCRE 




[AZURE.INCLUDE [app-analytics-footer](../../includes/app-analytics-footer.md)]

<!---HONumber=AcomDC_0309_2016-->