
<properties 
    pageTitle="Risoluzione dei problemi di creazione di raccolte ibride di RemoteApp di Azure "
    description="Informazioni su come risolvere i problemi relativi agli errori di creazione delle raccolte ibride di RemoteApp" 
    services="remoteapp" 
    solutions="" documentationCenter="" 
    authors="vkbucha" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="06/30/2015" 
    ms.author="elizapo" />



# Risoluzione dei problemi di creazione di raccolte ibride di Azure RemoteApp

Una raccolta ibrida è ospitata nel cloud di Azure insieme ai dati ma consente agli utenti di accedere anche ai dati e alle risorse archiviati nella rete locale. Gli utenti possono accedere alle app effettuando l'accesso con le credenziali aziendali sincronizzate o federate con Azure Active Directory. È possibile distribuire una raccolta ibrida che utilizza una rete virtuale di Azure esistente oppure è possibile creare una nuova rete virtuale. Si consiglia di creare o utilizzare una subnet di rete virtuale con un intervallo CIDR sufficientemente ampio per la crescita prevista per RemoteApp di Azure.

Se la raccolta non è stata ancora creata, vedere [Come creare una raccolta ibrida](remoteapp-create-hybrid-deployment.md)

Se si verificano problemi durante la creazione della raccolta o se la raccolta non funziona nel modo previsto, consultare le informazioni seguenti.

## La rete virtuale usa il tunneling forzato? ##
RemoteApp non supporta attualmente l'utilizzo di reti virtuali con tunneling forzato attivato. Se questa funzione è necessaria, contattare il team di RemoteApp per assistenza.

Dopo l’approvazione della richiesta, assicurarsi che le seguenti porte siano aperte nella subnet scelta per RemoteApp di Azure e le macchine virtuali nella subnet. Le macchine virtuali nelle subnet, inoltre, devono essere in grado di accedere gli URL indicati nella sezione sui gruppi di protezione di rete.

In uscita: TCP: 443, TCP: 10101-10175

## Per la rete virtuale sono definiti gruppi di protezione di rete? ##
Se sono stati definiti gruppi di protezione di rete nella subnet utilizzata per la raccolta, assicurarsi che gli URL seguenti siano accessibili dalla subnet:

	https://management.remoteapp.windowsazure.com  
	https://opsapi.mohoro.com  
	https://telemetry.remoteapp.windowsazure.com  
	https://*.remoteapp.windowsazure.com  
	https://login.windows.net (if you have Active Directory)  
	https://login.microsoftonline.com  
	Azure storage *.remoteapp.windowsazure.com  
	*.core.windows.net  
	https://www.remoteapp.windowsazure.com  
	https://www.remoteapp.windowsazure.com  

Aprire le porte seguenti nella subnet di rete virtuale:

In ingresso - TCP: 3030, TCP: 443 In uscita - TCP: 443

È possibile aggiungere gruppi di protezione di rete aggiuntivi alle macchine virtuali distribuite nella subnet per un maggiore controllo.

## Si utilizzano propri server DNS? Sono accessibili dalla subnet di rete virtuale? ##
Per le raccolte ibride è possibile utilizzare i propri server DNS. Specificarli nello schema di configurazione di rete o tramite il portale di gestione quando si crea la rete virtuale. I server DNS vengono utilizzati nell'ordine in cui vengono specificati in modo failover (anziché round robin).

Verificare che i server DNS per la raccolta siano accessibili e disponibili dalla subnet di rete virtuale specificata per questa raccolta.

ad esempio:

	<VirtualNetworkConfiguration>
    <Dns>
      <DnsServers>
        <DnsServer name="" IPAddress=""/>
      </DnsServers>
    </Dns>
	</VirtualNetworkConfiguration>

![Definire il DNS](./media/remoteapp-hybridtrouble/dnsvpn.png)

Per ulteriori informazioni, vedere [Risoluzione dei nomi utilizzando il proprio server DNS](https://msdn.microsoft.com/library/azure/jj156088.aspx#bkmk_BYODNS).

## Si sta utilizzando un controller di dominio di Active Directory nella raccolta? ##
Attualmente solo un dominio di Active Directory può essere associato a RemoteApp di Azure. La raccolta ibrida supporta solo gli account Azure Active Directory che sono stati sincronizzati mediante uno strumento come DirSync da una distribuzione di Windows Server Active Directory. In particolare, sincronizzati con l'opzione di sincronizzazione password oppure con la federazione di Active Directory Federation Services (ADFS) configurata. È necessario creare un dominio personalizzato che corrisponda al suffisso di dominio UPN per il dominio locale e impostare l'integrazione di directory.

Per informazioni sulla pianificazione, vedere [Configurare Active Directory per RemoteApp di Azure](remoteapp-ad.md).

Assicurarsi che i dettagli del dominio forniti siano validi e che il controller di dominio sia raggiungibile dalla macchina virtuale creata nella subnet utilizzata per RemoteApp di Azure. Assicurarsi inoltre che le credenziali dell'account di servizio fornite dispongano delle autorizzazioni per aggiungere computer al dominio specificato e che il nome di Active Directory specificato posa essere risolto dal DNS fornito nella rete virtuale.

## Quale nome di dominio è stato specificato quando è stata creata la raccolta? ##

Il nome di dominio creato o aggiunto deve essere un nome di dominio interno (non il nome di dominio Active Directory di Azure) e deve essere nel formato DNS risolvibile (contoso.local). Ad esempio, si dispone di un nome interno di Active Directory (contoso.local) e di un UPN di Directory Active (contoso.com): è necessario utilizzare il nome interno quando si crea la raccolta.

<!---HONumber=July15_HO1-->