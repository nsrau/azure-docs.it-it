
<properties
    pageTitle="Elenco di porte e URL da inserire nell'elenco di elementi consentiti per RemoteApp di Azure distribuito sulla rete virtuale cliente | Microsoft Azure"
    description="Informazioni sulle porte e gli URL da configurare per la comunicazione con RemoteApp di Azure."
    services="remoteapp"
	documentationCenter=""
    authors="mghosh1616"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="01/22/2016"
    ms.author="elizapo" />



# Elenco di porte e URL per consentire l'accesso a RemoteApp di Azure distribuito sulla rete virtuale cliente 

Questo si applica a una raccolta cloud o ibrida di RemoteApp di Azure in caso di distribuzione su una rete virtuale (VNET). Per ulteriori informazioni sulle reti virtuali, vedere la pagina di [panoramica sulle reti virtuali](virtual-networks-overview.md). Se è stato creato un gruppo di sicurezza di rete (NSG) che limita il traffico alle risorse di rete virtuale scelte per RemoteApp di Azure, verificare che le operazioni seguenti siano accessibili e consentite tramite i criteri di sicurezza della rete virtuale. Per ulteriori informazioni sui gruppi di sicurezza di rete, leggere [Che cos'è un gruppo di sicurezza di rete? (NSG)](virtual-networks-nsg.md).

##  La subnet di RemoteApp di Azure necessita dell'accesso ai seguenti endpoint e URL: 
*	**.servicebus.windows.net
*	 **. servicebus.net
*	 https://*.remoteapp.windwsazure.com  
*	 https://www.remoteapp.windowsazure.com 
*	 https://*remoteapp.windowsazure.com  
*	 https://*.core.windows.net  
*	 In uscita: TCP: 443, TCP: 10101-10175 
*	 Facoltativo: UDP: 10201-10275  
 
## I client RemoteApp di Azure devono accedere ai seguenti endpoint e URL: 

Per client si intende desktop, dispositivi e così via, usati per connettersi alle app distribuite nella raccolta RemoteApp di Azure.

-  https://telemetry.remoteapp.windowsazure.com  
-  https://**.remoteapp.windowsazure.com (le porte UDP facoltative riguardano questo indirizzo) 
-  https://login.windows.net  
-  https://login.microsoftonline.com  
-  https://www.remoteapp.windowsazure.com 
-  https://**.remoteapp.windowsazure.com  
-  https://*.core.windows.net  
-  In uscita: TCP: 443  
-  Facoltativo: UDP: 3391 

<!---HONumber=AcomDC_0128_2016-->