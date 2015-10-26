<properties 
                pageTitle="Informazioni sull'accesso alle risorse in Azure" 
                description="Questo argomento illustra i concetti relativi all'uso di amministratori della sottoscrizione per controllare l'accesso alle risorse nel portale di Azure completo." 
                services="active-directory" 
                documentationCenter="" 
                authors="markusvi" 
                manager="swadhwa" 
                editor=""/>

<tags 
                ms.service="active-directory" 
                ms.workload="identity" 
                ms.tgt_pltfrm="na" 
                ms.devlang="na" 
                ms.topic="article" 
                ms.date="08/10/2015" 
                ms.author="markusvi"/>


# Informazioni sull'accesso alle risorse in Azure


> [AZURE.NOTE]Questo argomento illustra i concetti relativi all'uso di amministratori della sottoscrizione per controllare l'accesso alle risorse nel portale di Azure completo. In alternativa, il portale di anteprima di Azure fornisce la funzionalità di [controllo degli accessi in base al ruolo](role-based-access-control-configure.md) che permette di gestire le risorse di Azure con maggiore precisione.

Nel mese di ottobre 2013 il portale di gestione di Azure e le API di gestione dei servizi sono state integrate con Azure Active Directory al fine di creare i presupposti per migliorare l'esperienza utente per la gestione degli accessi alle risorse di Azure. Azure Active Directory offre già potenti funzionalità per la gestione degli utenti, per la sincronizzazione della directory locale, per l'autenticazione a più fattori e il controllo di accesso alle applicazioni. Naturalmente, queste funzionalità dovrebbero essere rese disponibili per la gestione delle risorse di Azure a livello generale.

La fatturazione costituisce il punto di partenza per il controllo di accesso in Azure. Il proprietario di un account Azure, a cui si accede tramite il [Centro account di Azure](https://account.windowsazure.com/subscriptions), è l'amministratore account. Le sottoscrizioni rappresentano un contenitore per la fatturazione, ma fungono anche da limite di sicurezza: ogni sottoscrizione deve avere un amministratore dei servizi che può aggiungere, rimuovere, modificare le risorse di Azure incluse in quella sottoscrizione specifica tramite il [portale di gestione di Azure](https://manage.windowsazure.com/). L'amministratore dei servizi predefinito di una nuova sottoscrizione è l'amministratore dell'account, che, a sua volta, può assegnare il ruolo di amministratore dei servizi a un altro utente nel Centro account di Azure.
 
<br><br>![Account Azure][1]

Le sottoscrizioni sono anche associate a una directory. Una directory definisce un set di utenti. Possono essere utenti dell'azienda o dell'istituto di istruzione che ha creato la directory o utenti esterni, ovvero utenti con un account Microsoft. Le sottoscrizioni sono accessibili da un sottoinsieme di utenti della directory alla quale sono stati assegnati come amministratore dei servizi o coamministratore. Fanno eccezione, per motivi che risalgono al passato, gli account Microsoft (in precedenza Windows Live ID), i quali possono essere assegnati come amministratore dei servizi o coamministratore senza essere presenti nella directory.

<br><br>![Controllo di accesso in Azure][2]

 
Le funzionalità incluse nel portale di gestione di Azure consentono agli amministratori dei servizi che hanno eseguito l'accesso con un account Microsoft di modificare la directory a cui è associata la sottoscrizione tramite il comando **Modifica directory** nella sezione **Impostazioni** della pagina **Sottoscrizioni**. Si noti che questa operazione può influire sul controllo di accesso di quella sottoscrizione.



> [AZURE.NOTE]Il comando **Modifica directory** nel portale di gestione di Azure non è disponibile per gli utenti che hanno eseguito l'accesso con un account aziendale o dell'istituto di istruzione, perché questi account possono accedere solo alla directory a cui appartengono.

<br><br>![Flusso di accesso utente semplice][3]

Nel caso più semplice, un'organizzazione, ad esempio Contoso, imporrà la fatturazione e il controllo di accesso a livello dello stesso set di sottoscrizioni. Questo significa che la directory è associata alle sottoscrizioni di proprietà di un singolo account Azure. Dopo l'accesso al portale di gestione di Azure, gli utenti visualizzeranno due raccolte di risorse (illustrate in arancione nella figura precedente):


- Directory in cui è presente l'account utente (originato o aggiunto come entità esterna). Si noti che la directory usata per l'accesso non è rilevante ai fini di questo calcolo, di conseguenza verranno sempre visualizzate tutte le directory, indipendentemente da quella usata per l'accesso.

- Risorse che fanno parte delle sottoscrizioni associate alla directory usata per l'accesso E a cui l'utente può accedere (perché è amministratore dei servizi o coamministratore).


<br><br>![Utente con più sottoscrizioni e directory][4]


Gli utenti con sottoscrizioni in più directory possono cambiare il contesto corrente del portale di gestione di Azure tramite il filtro della sottoscrizione. Questo comporta un accesso separato a una directory diversa, operazione che viene eseguita automaticamente tramite l'accesso Single Sign-On.

Operazioni quali lo spostamento di risorse tra sottoscrizioni possono risultare più difficoltose a causa della visualizzazione a directory singola delle sottoscrizioni. Per procedere al trasferimento di risorse, potrebbe essere necessario usare prima il comando **Modifica directory** nella sezione **Impostazioni** della pagina Sottoscrizioni per associare le sottoscrizioni alla stessa directory.



<!--Image references-->
[1]: ./media/active-directory-understanding-resource-access/IC707931.png
[2]: ./media/active-directory-understanding-resource-access/IC707932.png
[3]: ./media/active-directory-understanding-resource-access/IC707933.png
[4]: ./media/active-directory-understanding-resource-access/IC707934.png

<!---HONumber=Oct15_HO3-->