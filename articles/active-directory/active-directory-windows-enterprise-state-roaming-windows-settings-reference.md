<properties
	pageTitle="Riferimento alle impostazioni di roaming di Windows 10 | Microsoft Azure"
	description="Elenco completo di tutte le impostazioni di cui verrà effettuato il roaming o il backup in Windows 10."
	services="active-directory"
    keywords="enterprise state roaming, cloud windows"
	documentationCenter=""
	authors="femila"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory"  
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/04/2016"
	ms.author="femila"/>

# Riferimento alle impostazioni di roaming di Windows 10

Di seguito è riportato un elenco completo di tutte le impostazioni di cui verrà effettuato il roaming o il backup in Windows 10.

##Dispositivi ed endpoint
La tabella seguente contiene un riepilogo dei dispositivi e dei tipi di account supportati dal framework di sincronizzazione, backup e ripristino in Windows 10.

| Tipo di account e operazione | Desktop | Mobile |
|----------------------------|---------|--------|
|Azure Active Directory: sincronizzazione| Sì | No |
|Azure Active Directory: backup e ripristino| No | No |
|Account Microsoft: sincronizzazione|Sì | Sì |
|Account Microsoft: backup e ripristino| No | Sì |


##Cosa si intende per backup
Le impostazioni di Windows vengono in genere sincronizzate per impostazione predefinita. Di alcune impostazioni, come ad esempio l'elenco delle applicazioni installate in un dispositivo, viene tuttavia eseguito solo il backup. Se un utente disabilita la sincronizzazione nel dispositivo usando l'app Impostazioni, i dati dell'applicazione che normalmente vengono sincronizzati diventano solo di backup. L'accesso ai dati di backup è possibile solo con l'operazione di ripristino durante la prima esecuzione di un nuovo dispositivo. I backup possono essere disattivati con le impostazioni del dispositivo e possono essere gestiti ed eliminati tramite l'account OneDrive dell'utente.

## Panoramica sulle impostazioni di Windows
I gruppi di impostazioni seguenti permettono agli utenti finali di abilitare o disabilitare la sincronizzazione delle impostazioni nei dispositivi Windows 10.

- Tema: sfondo del desktop, icona utente, posizione della barra delle applicazioni e così via. 
- Impostazioni di Internet Explorer: cronologia esplorazioni, URL tipizzati, Preferiti e così via. 
- Password: [Casella di sicurezza delle credenziali di Windows](https://technet.microsoft.com/library/jj554668.aspx), inclusi i profili Wi-Fi. 
- Preferenze lingua: dizionario per il controllo ortografico, impostazioni della lingua del sistema. 
- Accessibilità: Assistente vocale, tastiera su schermo, lente di ingrandimento. 
- Altre impostazioni di Windows: vedere la sezione Dettagli relativi alle impostazioni di Windows.

![](./media/active-directory-enterprise-state-roaming/active-directory-enterprise-state-roaming-individual-sync-settings.png)
 
## Dettagli relativi alle impostazioni di Windows
Nella tabella seguente, la voce Altre nella colonna Gruppo di impostazioni fa riferimento alle impostazioni che possono essere disabilitate da Impostazioni > Account > Sincronizza le impostazioni > Altre impostazioni di Windows.

La voce Interne nella colonna Gruppo di impostazioni fa riferimento alle impostazioni e alle app che possono essere disabilitate soltanto tramite la sincronizzazione dall'interno dell'app stessa o disabilitando la sincronizzazione per l'intero dispositivo con la gestione dei dispositivi mobili (software MDM) o le impostazioni dei Criteri di gruppo. Le impostazioni di cui non viene effettuato il roaming o la sincronizzazione non appartengono a un gruppo.


| Impostazioni | Desktop | Mobile | Group |
|----------------------------------|---------|---------|-------|
| **Account**: immagine dell'account | sincronizzazione |X |Tema |
| **Account**: altre impostazioni account |X |X | |
| **Banda larga mobile avanzata**: nome della rete di Condivisione connessione Internet, che consente il rilevamento automatico degli hotspot Wi-Fi per dispositivi mobili tramite Bluetooth|sincronizzazione |sincronizzazione |Password |
|**Dati app**: singole app possono sincronizzare i dati|backup sincronizzazione | backup sincronizzazione|Interne |
|**Elenco app**: elenco delle app installate |X |backup |Altre |
|**Bluetooth**: tutte le impostazioni Bluetooth |X |X | |
|**Prompt dei comandi**: tutte le impostazioni del prompt dei comandi |sincronizzazione| |X |Altre
|**Cortana**: abilitazione/disabilitazione |X |X | |
|**Cortana**: abilitazione di Cortana nella schermata di blocco |X |X | |
|**Cortana**: nome utente |sincronizzazione |sincronizzazione |Interne|
|**Cortana**: lettura di SMS |X |sincronizzazione |Interne|
|**Cortana**: Ricerca sicura |X |sincronizzazione |Interne|
|**Cortana**: ricerca di informazioni sui voli e altro|X |sincronizzazione |Interne|
|**Credenziali**: Casella di sicurezza delle credenziali |sincronizzazione |sincronizzazione |password|
|**Data, ora e opzioni internazionali**: ora automatica (sincronizzazione con l'ora Internet) |sincronizzazione |sincronizzazione |Lingua|
|**Data, ora e opzioni internazionali**: formato a 24 ore|sincronizzazione |sincronizzazione |Lingua|
|**Data, ora e opzioni internazionali**: data e ora|sincronizzazione |X |Lingua|
|**Data, ora e opzioni internazionali**: fuso orario | |X |Lingua|
|**Data, ora e opzioni internazionali**: ora legale|sincronizzazione |X |Lingua|
|**Data, ora e opzioni internazionali**: paese/area geografica |sincronizzazione |X |Lingua|
|**Data, ora e opzioni internazionali**: primo giorno della settimana |sincronizzazione |X |Lingua|
|**Data, ora e opzioni internazionali**: formato regionale (impostazioni locali) |sincronizzazione |X |Lingua|
|**Data, ora e opzioni internazionali**: data breve |sincronizzazione |X |Lingua|
|**Data, ora e opzioni internazionali**: data estesa |sincronizzazione |X |Lingua|
|**Data, ora e opzioni internazionali**: ora breve |sincronizzazione |X |Lingua|
|**Data, ora e opzioni internazionali**: ora estesa |sincronizzazione |X |Lingua|
|**Personalizzazione del desktop**: tema del desktop (sfondo, colore di sistema, segnali acustici sistema predefiniti, screen saver) |sincronizzazione |X |Tema|
|**Personalizzazione del desktop**: sfondo presentazione |sincronizzazione |X |Tema|
|**Personalizzazione del desktop**: impostazioni della barra delle applicazioni (posizione, Nascondi automaticamente e così via) |sincronizzazione |X |Tema|
|**Personalizzazione del desktop**: layout della schermata Start |sincronizzazione |backup ||
|**Dispositivi**: stampanti condivise a cui è stata eseguita la connessione |sincronizzazione | X |Altro |
|**Browser Edge**: Elenco di lettura |sincronizzazione |sincronizzazione |Interne|
|**Browser Edge**: Preferiti |sincronizzazione |sincronizzazione |Interne|
|**Browser Edge**: tutte le altre impostazioni di Edge|X |X ||
|**Contrasto elevato**: abilitazione/disabilitazione |sincronizzazione |sincronizzazione |Accessibilità|
|**Contrasto elevato**: impostazioni del tema|sincronizzazione |X ||Accessibilità|
|**Internet Explorer**: schede aperte (URL e titolo)|sincronizzazione |sincronizzazione |Internet Explorer|
|**Internet Explorer**: Elenco di lettura|sincronizzazione |sincronizzazione |Internet Explorer|
|**Internet Explorer**: URL digitati|sincronizzazione |sincronizzazione |Internet Explorer|
|**Internet Explorer**: cronologia esplorazioni|sincronizzazione |sincronizzazione |Internet Explorer|
|**Internet Explorer**: Preferiti|sincronizzazione |sincronizzazione |Internet Explorer|
|**Internet Explorer**: URL esclusi|sincronizzazione |sincronizzazione |Internet Explorer|
|**Internet Explorer**: home page|sincronizzazione |sincronizzazione |Internet Explorer|
|**Internet Explorer**: suggerimenti dominio|sincronizzazione |sincronizzazione |Internet Explorer|
|**Tastiera**: abilitazione/disabilitazione della tastiera su schermo|sincronizzazione |X |Accessibilità|
|**Tastiera**: Attiva Tasti permanenti (disabilitati per impostazione predefinita)|sincronizzazione |X |Accessibilità|
|**Tastiera**: Attiva Filtro tasti (disabilitato per impostazione predefinita)|sincronizzazione |X |Accessibilità|
|**Tastiera**: Attiva Segnali acustici (disabilitati per impostazione predefinita)|sincronizzazione |X |Accessibilità|
|**Internet Explorer**: cinese (QWERTY), abilitazione dell'apprendimento automatico della lingua del dominio|sincronizzazione |X |Lingua|
|**Lingua**: cinese (QWERTY), abilitazione della classificazione dinamica dei candidati|sincronizzazione |X |Lingua|
|**Lingua**: cinese (QWERTY), set di caratteri cinese semplificato|sincronizzazione |X |Lingua|
|**Lingua**: cinese (QWERTY), set di caratteri cinese tradizionale|sincronizzazione |X |Lingua|
|**Lingua**: cinese (QWERTY), Pinyin fuzzy|sincronizzazione |sincronizzazione |Lingua|
|**Lingua**: cinese (QWERTY), coppie fuzzy|sincronizzazione |sincronizzazione |Lingua|
|**Lingua**: cinese (QWERTY), Pinyin completo||sincronizzazione |X |Lingua|
|**Lingua**: cinese (QWERTY), Pinyin doppio|sincronizzazione |X |Lingua|
|**Lingua**: cinese (QWERTY), correzione automatica lettura|sincronizzazione |X |Lingua|
|**Lingua**: cinese (QWERTY), cambio tastiera C/E, MAIUSC|sincronizzazione |X |Lingua|
|**Lingua**: cinese (QWERTY), cambio tastiera C/E, CTRL|sincronizzazione |X |Lingua|
|**Lingua**: cinese (WUBI), Modalità di input a singolo carattere |sincronizzazione |X |Lingua|
|**Lingua**: cinese (WUBI), Mostra il codice rimanente dei candidati |sincronizzazione |X |Lingua|
|**Lingua**: cinese (WUBI), Emetti un segnale acustico se la codifica 4 non è valida|sincronizzazione |X |Lingua|
|**Lingua**: Bopomofo cinese, inclusione di CJK, estensione A|sincronizzazione |X |Lingua|
|**Lingua**: IME giapponese, digitazione predittiva e parole personalizzate|sincronizzazione |sincronizzazione |Lingua|
|**Lingua**: IME coreano|X |X |Lingua|
|**Lingua**: riconoscimento della grafia|X |X |Lingua|
|**Lingua**: profilo lingua|sincronizzazione |backup |Lingua|
|**Lingua**: controllo ortografico, correzione automatica ed evidenziazione degli errori di ortografia|sincronizzazione |backup |Lingua|
|**Lingua**: elenco delle tastiere|sincronizzazione |backup |Lingua|
|**Schermata di blocco**: tutte le impostazioni della schermata di blocco|X |X ||
|**Lente di ingrandimento**: abilitazione/disabilitazione (master)|X |backup |Accessibilità|
|**Lente di ingrandimento**: Attiva inversione colori (disattivata per impostazione predefinita)|sincronizzazione |X |Accessibilità|
|**Lente di ingrandimento**: rilevamento, Segui la tastiera|sincronizzazione |X |Accessibilità|
|**Lente di ingrandimento**: rilevamento, Segui il puntatore del mouse|sincronizzazione |X |Accessibilità|
|**Lente di ingrandimento**: avvio all'accesso dell'utente (disabilitato per impostazione predefinita)|sincronizzazione |X |Accessibilità|
|**Mouse**: modifica delle dimensioni del cursore del mouse|sincronizzazione |X |Altro|
|**Mouse**: modifica del colore del cursore del mouse|sincronizzazione |X |Altro|
|**Mouse**: tutte le altre impostazioni|X |X ||
|**Assistente vocale**: avvio veloce|sincronizzazione |X |Accessibilità|
|**Assistente vocale**: gli utenti possono modificare la tonalità di voce dell'Assistente vocale|sincronizzazione |X |Accessibilità|
|**Assistente vocale**: gli utenti possono abilitare/disabilitare la lettura di suggerimenti per gli elementi comuni da parte dell'Assistente vocale (abilitata per impostazione predefinita)|sincronizzazione |X |Accessibilità|
|**Assistente vocale**: gli utenti possono abilitare/disabilitare la lettura dei caratteri digitati (abilitata per impostazione predefinita)|sincronizzazione |X |Accessibilità|
|**Assistente vocale**: gli utenti possono abilitare/disabilitare la lettura delle parole digitate (abilitata per impostazione predefinita)|sincronizzazione |X |Accessibilità|
|**Assistente vocale**: il cursore di inserimento segue l'Assistente vocale (abilitata per impostazione predefinita)|sincronizzazione |X |Accessibilità|
|**Assistente vocale**: Abilita evidenziazione visiva del cursore dell'Assistente vocale (abilitata per impostazione predefinita)|sincronizzazione |X |Accessibilità|
|**Assistente vocale**: Riproduci segnali acustici (abilitata per impostazione predefinita)|sincronizzazione |X |Accessibilità|
|**Assistente vocale**: Attiva tasti della tastiera virtuale quando l'utente solleva il dito (disattivata per impostazione predefinita)|sincronizzazione |sincronizzazione |Accessibilità|
|**Accessibilità**: Imposta lo spessore del cursore intermittente|sincronizzazione |X |Accessibilità|
|**Accessibilità**: Rimuovi immagini di sfondo (disabilitata per impostazione predefinita)|sincronizzazione |X |Accessibilità|
|**Alimentazione e sospensione**: tutte le impostazioni|X |X ||
|**Personalizzazione della schermata Start**: colore di sistema|sincronizzazione |sincronizzazione |Tema|
|**Digitazione**: dizionario per il controllo ortografico|sincronizzazione |backup |Lingua|
|**Digitazione**: Correggi automaticamente errori di ortografia|sincronizzazione |backup |Lingua|
|**Digitazione**: Evidenzia errori di ortografia|sincronizzazione |backup |Lingua|
|**Digitazione**: Mostra suggerimenti di testo durante la digitazione|sincronizzazione |backup |Lingua|
|**Digitazione**: Aggiungi uno spazio quando scelgo un suggerimento di testo|sincronizzazione |backup |Lingua|
|**Digitazione**: Aggiungi un punto dopo un doppio tocco sulla BARRA SPAZIATRICE|sincronizzazione |backup |Lingua|
|**Digitazione**: Applica maiuscola a inizio di frase|sincronizzazione |backup |Lingua|
|**Digitazione**: Usa tutte maiuscole dopo doppio tocco del tasto MAIUSC|sincronizzazione |backup |Lingua|
|**Digitazione**: Riproduci suoni tasti durante la digitazione|sincronizzazione |backup |Lingua|
|**Digitazione**: dati di personalizzazione per la tastiera virtuale|sincronizzazione |backup |Lingua|
|**Wi-Fi**: profili Wi-Fi (solo WPA)|sincronizzazione |sincronizzazione |Password|


## Argomenti correlati
- [Panoramica di Enterprise State Roaming](active-directory-windows-enterprise-state-roaming-overview.md)
- [Abilitare Enterprise State Roaming in Azure Active Directory](active-directory-windows-enterprise-state-roaming-enable.md)
- [Domande frequenti su impostazioni e dati in roaming](active-directory-windows-enterprise-state-roaming-faqs.md)
- [Criteri di gruppo e impostazioni del software MDM per la sincronizzazione delle impostazioni](active-directory-windows-enterprise-state-roaming-group-policy-settings.md)






  

<!---HONumber=AcomDC_0204_2016-->