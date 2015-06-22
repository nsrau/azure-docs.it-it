
<properties 
    pageTitle="Risolvere i problemi delle raccolte ibride - creazione"
    description="Informazioni su come risolvere i problemi relativi agli errori di creazione delle raccolte ibride di RemoteApp" 
    services="remoteapp" 
    solutions="" documentationCenter="" 
    authors="vkbucha" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="02/20/2015" 
    ms.author="vikbucha" />



# Risolvere i problemi delle raccolte ibride - creazione

Prima di risolvere gli errori che si verificano durante la creazione delle raccolte ibride, è utile comprendere in che modo vengono create le raccolte ibride. Una raccolta ibrida richiede che le istanze di RemoteApp siano aggiunte a un dominio. Questa operazione viene eseguita durante la creazione della raccolta.  Quando il processo di creazione della raccolta viene avviato, vengono create delle copie delle immagini modello caricate nella rete virtuale che, mediante un tunnel VPN da sito a sito, vengono aggiunte a un dominio risolto dal record IP DNS specificato durante la creazione della rete virtuale.

Errori più frequenti visualizzati nel portale di gestione di Azure:

	DNS server could not be reached

	Could not join the domain. Unable to reach the domain.

Se si verifica uno degli errori riportati in precedenza, verificare quanto segue:

- Verificare che le configurazioni IP DNS siano valide
- Verificare che i record IP DNS siano record IP pubblici o facciano parte dello "spazio di indirizzi locale" specificato durante la creazione della rete virtuale
- Verificare il tunnel della rete virtuale per assicurarsi che sia in stato attivo o connesso 
- Verificare che il lato locale della connessione VPN non stia bloccando il traffico di rete. Per farlo, individuare i log del dispositivo VPN locale o del software.
- Verificare che il dominio specificato durante la creazione della raccolta sia attivo e in esecuzione.

	ProvisioningTimeout


Se viene visualizzato questo errore, verificare quanto segue:

- Verificare che il lato locale della connessione VPN non stia bloccando il traffico di rete. Per farlo, individuare i log del dispositivo VPN locale o del software.
- Verificare che l'immagine modello di RemoteApp caricata sia stata preparata correttamente con SYSPREP. I requisiti dell'immagine di RemoteApp possono essere controllati da qui: http://azure.microsoft.com/documentation/articles/remoteapp-create-custom-image/ 
- Provare a creare una macchina virtuale usando l'immagine modello caricata e verificare che venga avviata ed eseguita correttamente (a) in un server Hyper-V locale oppure (b) creando una macchina virtuale Azure IAAS nella sottoscrizione di Azure. Errori nella creazione o nell'avvio della macchina virtuale indicano, in genere, che l'immagine modello non è stata preparata correttamente ed è necessario correggerla.

	DomainJoinFailed_InvalidUserNameOrPassword

	DomainJoinFailed_InvalidParameter

Se si verifica uno degli errori riportati in precedenza, verificare quanto segue:

- Verificare che le credenziali immesse per l'aggiunta a un dominio siano valide
- Verificare che le credenziali per l'aggiunta a un dominio siano corrette o di disporre delle autorizzazioni per l'aggiunta a un dominio appropriate
- Verificare che l'unità organizzativa sia formattata correttamente e non esista in Active Directory.


<!--HONumber=52--> 