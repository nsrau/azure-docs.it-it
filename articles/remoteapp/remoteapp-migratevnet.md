<properties 
    pageTitle="Come eseguire la migrazione da una rete virtuale RemoteApp a una rete virtuale di Azure"
    description="Informazioni su come eseguire la migrazione da una rete virtuale RemoteApp a una rete virtuale di Azure" 
    services="remoteapp" 
	documentationCenter="" 
    authors="lizap" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="05/28/2015" 
    ms.author="elizapo" />



# Come eseguire la migrazione di una raccolta ibrida da una rete virtuale RemoteApp a una rete virtuale di Azure

Ottime notizie! Ora è possibile distribuire raccolte ibride di RemoteApp direttamente nelle reti virtuali di Azure esistenti invece di creare reti virtuali RemoteApp specifiche. In questo modo è possibile sfruttare le nuove funzionalità delle reti virtuali (come ExpressRoute) e concedere alle raccolte ibride l'accesso diretto alla rete per altri servizi e macchine virtuali di Azure distribuiti in a tale rete virtuale. È così possibile migliorare le prestazioni e facilitare l'installazione rispetto alle configurazioni tra reti virtuali.


Si supponga di avere già creato una raccolta RemoteApp ibrida denominata *RaccoltaOriginale* con una rete virtuale RemoteApp denominata *ReteVirtualeRemoteApp*. Di seguito è riportata la procedura per eseguirne la migrazione a una nuova rete virtuale di Azure denominata *ReteVirtualeAzure*.

1.	Nella scheda **Reti** del [portale di gestione](http://manage.windowsazure.com/) creare una rete virtuale denominata *ReteVirtualeAzure* usando lo stesso percorso, la stessa configurazione del DNS e lo stesso spazio di indirizzi (per almeno una delle subnet di *ReteVirtualeAzure*) usati per *ReteVirtualeRemoteApp*.
2.	Configurare *ReteVirtualeAzure* per ospitare o avere la connettività di rete alla distribuzione di Active Directory in cui *RaccoltaOriginale* è aggiunta al dominio.
3.	Nella scheda **RemoteApp** creare una nuova raccolta RemoteApp denominata *NuovaRaccolta*. Usare l'opzione **Crea con rete virtuale**, non **Creazione rapida**.
3.	Configurare *NuovaRaccolta* per la distribuzione a una subnet in *ReteVirtualeAzure*.
4.	Configurare *NuovaRaccolta* per l'uso della stessa immagine e delle stesse informazioni sull'aggiunga a un dominio usate per *RaccoltaOriginale*.
5.	Dopo alcune ore, *NuovaRaccolta* verrà visualizzata nell'elenco di raccolte con uno stato attivo.

Se NON è necessario eseguire la migrazione delle informazioni utente dalla raccolta originale alla nuova, eseguire i passaggi seguenti:

6.	Eliminare *RaccoltaOriginale*.
7.	Eliminare *ReteVirtualeRemoteApp*.

L'operazione è così completata.

In alternativa, se È necessario eseguire la migrazione delle informazioni utente dalla raccolta originale alla nuova, eseguire i passaggi seguenti:

6.	Inviare un messaggio di posta elettronica a [remoteappforum@microsoft.com](mailto:remoteappforum@microsoft.com?subject=Azure%20RemoteApp%20user%20information%20migration) con l'ID sottoscrizione di Azure, il nome della raccolta originale e il nome della nuova raccolta e richiedere la migrazione delle informazioni utente.
7.	Entro due giorni lavorativi il team di RemoteApp sposterà l'elenco di accesso degli utenti e tutti i documenti e le impostazioni degli utenti dalla raccolta originale alla nuova.
8.	Eliminare *RaccoltaOriginale*.
9.	Eliminare *ReteVirtualeRemoteApp*.

L'operazione è così completata.

In caso di domande o se è necessaria un'assistenza particolare, inviare un messaggio di posta elettronica a [remoteappforum@microsoft.com](mailto:remoteappforum@microsoft.com?subject=Azure%20RemoteApp%20VNET%20migration%20help).
 

<!---HONumber=July15_HO4-->