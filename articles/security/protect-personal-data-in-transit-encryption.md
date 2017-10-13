---
title: Proteggere i dati personali in transito con la crittografia in Azure | Microsoft Docs
description: Uso della crittografia in Azure per proteggere i dati personali
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomSh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/22/2017
ms.author: barclayn
ms.custom: 
ms.openlocfilehash: 461ddfda796bfe6639e27f6c4cd53e82b4e397d1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-encryption-technologies-protect-personal-data-in-transit-with-encryption"></a>Tecnologie di crittografia di Azure: proteggere i dati personali in transito con la crittografia

Questo articolo aiuta a comprendere e usare le tecnologie di crittografia di Azure per proteggere i dati in transito. 

La protezione della privacy dei dati personali mentre attraversano la rete è una parte essenziale di una strategia di sicurezza con difesa avanzata a più livelli. La crittografia dei dati in transito è progettata per impedire a un utente malintenzionato che intercetta le trasmissioni di visualizzare o usare i dati.

## <a name="scenario"></a>Scenario

Un'importante compagnia di viaggi in crociera, con sede negli Stati Uniti, sta espandendo le proprie operazioni per offrire itinerari nel Mar Mediterraneo e nel Mar Baltico, nonché nelle isole britanniche. Per supportare tale iniziativa, ha acquistato diverse linee minori con sede in Italia, Germania, Danimarca e Regno Unito. 

La società usa Microsoft Azure per archiviare i dati aziendali nel cloud. Questi includono informazioni personali come nomi, indirizzi, numeri di telefono e dati delle carte di credito della base clienti globale, nonché informazioni tradizionali del reparto risorse umane come indirizzi, numeri di telefono, codici fiscali e altri dati dei dipendenti aziendali in tutte le sedi. La linea di crociere gestisce anche un database di grandi dimensioni dei membri dei programmi fedeltà e premi, che include informazioni personali per tenere traccia delle relazioni con i clienti attuali e del passato.

I dati personali dei clienti vengono immessi nel database da uffici remoti dell'azienda e agenti di viaggio in tutto il mondo. I documenti contenenti le informazioni relative ai clienti vengono trasferiti in Archiviazione di Azure tramite la rete.

## <a name="problem-statement"></a>Presentazione del problema

L'azienda deve proteggere la privacy dei dati personali dei clienti e dei dipendenti mentre sono in transito da e verso i servizi di Azure.

## <a name="company-goal"></a>Obiettivo dell'azienda

L'obiettivo dell'azienda è garantire la crittografia dei dati personali quando non sono su disco. Se utenti non autorizzati intercettano i dati personali che non si trovano su disco, questi devono essere in un formato che li renda illeggibili. L'applicazione della crittografia deve essere facile o completamente trasparente per utenti e amministratori.

## <a name="solutions"></a>Soluzioni

I servizi di Azure offrono più strumenti e tecnologie per la protezione dei dati personali in transito.

### <a name="azure-storage"></a>Archiviazione di Azure

I dati che vengono archiviati nel cloud devono essere trasferiti dal client, che può trovarsi fisicamente ovunque nel mondo, al data center di Azure e quindi nella direzione opposta quando vengono recuperati dagli utenti. I dati in transito tramite Internet pubblico sono sempre a rischio di intercettazione da parte di utenti malintenzionati. È importante proteggere la privacy dei dati personali usando la crittografia a livello di trasporto per proteggere i dati nello spostamento da una posizione a un'altra.

Il protocollo HTTPS offre un canale di comunicazione crittografato e sicuro su Internet. È consigliabile usare HTTPS per accedere agli oggetti in Archiviazione di Azure e per la chiamata di API REST. L'uso del protocollo HTTPS viene imposto quando si usano [firme di accesso condiviso](https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1) per delegare l'accesso agli oggetti di Archiviazione di Azure. Esistono due tipi di firma di accesso condiviso: del servizio e dell'account.

#### <a name="how-do-i-construct-a-service-sas"></a>Come creare una firma di accesso condiviso del servizio

Una firma di accesso condiviso del servizio delega l'accesso a una risorsa in uno solo dei servizi di archiviazione (servizio BLOB, code, tabelle o file). Per creare una firma di accesso condiviso del servizio, seguire questa procedura:

1. Specificare il campo della versione firmata

2. Specificare la risorsa firmata (solo servizio file e BLOB)

3. Specificare parametri di query per sostituire le intestazioni delle risposte (solo servizio file e BLOB)

4. Specificare il nome della tabella (solo servizio tabelle)

5. Specificare i criteri di accesso

6. Specificare l'intervallo di validità della firma

8. Specificare le autorizzazioni

9. Specificare l'indirizzo IP o l'intervallo IP

10. Specificare il protocollo HTTP

11. Specificare gli intervalli di accesso delle tabelle

12. Specificare l'identificatore firmato

13. Specifica la firma

Per istruzioni più dettagliate, vedere [Constructing a Service SAS](https://docs.microsoft.com/rest/api/storageservices/Constructing-a-Service-SAS?redirectedfrom=MSDN) (Creazione di una firma di accesso condiviso del servizio).

#### <a name="how-do-i-construct-an-account-sas"></a>Come creare una firma di accesso condiviso dell'account

Una firma di accesso condiviso dell'account delega l'accesso alle risorse in uno o più servizi di archiviazione. È anche possibile delegare l'accesso alle operazioni di lettura, scrittura ed eliminazioni in contenitori BLOB, tabelle, code e condivisioni file, che con una firma di accesso condiviso del servizio non è consentito. La creazione di una firma di accesso condiviso dell'account è simile a quella di una firma di accesso condiviso del servizio. Per istruzioni dettagliate, vedere [Constructing an Account SAS](https://docs.microsoft.com/rest/api/storageservices/Constructing-an-Account-SAS?redirectedfrom=MSDN) (Creazione di una firma di accesso condiviso dell'account).

#### <a name="how-do-i-enforce-https-when-calling-rest-apis"></a>Come imporre HTTPS nella chiamata di API REST

Per imporre l'uso di HTTPS quando si chiamano le API REST per accedere a oggetti negli account di archiviazione, è possibile abilitare l'opzione Trasferimento sicuro obbligatorio per l'account di archiviazione. 

1. Nel portale di Azure selezionare **Crea account di archiviazione** oppure, per un account di archiviazione esistente, selezionare **Impostazioni** e quindi **Configurazione**.

2. In **Trasferimento sicuro obbligatorio** selezionare **Abilitato**.

![Creazione di un account di archiviazione](media/protect-personal-data-in-transit-encryption/create-storage-account.png)

Per istruzioni più dettagliate, ad esempio su come abilitare l'opzione Trasferimento sicuro obbligatorio a livello di codice, vedere [Require Secure Transfer](https://docs.microsoft.com/azure/storage/storage-require-secure-transfer) (Richiedere il trasferimento sicuro).

#### <a name="how-do-i-encrypt-data-in-azure-file-storage"></a>Come crittografare i dati in Archiviazione file di Azure

Per crittografare i dati in transito con [Archiviazione file di Azure](https://docs.microsoft.com/azure/storage/storage-file-how-to-use-files-portal), è possibile usare SMB 3.x con Windows 8, 8.1 e 10 e con Windows Server 2012 R2 e Windows Server 2016. Quando si usa il servizio File di Azure, se è abilitata l'opzione "Trasferimento sicuro obbligatorio" le connessioni senza crittografia hanno esito negativo. Questo include scenari in cui si usano SMB 2.1, SMB 3.0 senza crittografia e alcuni tipi del client SMB Linux.

#### <a name="azure-client-side-encryption"></a>Crittografia lato client di Azure

Un'altra opzione per proteggere i dati personali durante il trasferimento tra un'applicazione client e Archiviazione di Azure è la [crittografia lato client](https://docs.microsoft.com/azure/storage/storage-client-side-encryption). I dati vengono crittografati prima del trasferimento in Archiviazione di Azure e decrittografati dopo la ricezione sul lato client quando si recuperano da Archiviazione di Azure.

### <a name="azure-site-to-site-vpn"></a>VPN da sito a sito di Azure

Un modo efficace per proteggere i dati in transito tra un utente o una rete aziendale e la rete virtuale di Azure consiste nell'usare una rete privata virtuale (VPN) [da sito a sito](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal) o [da punto a sito](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal). Una connessione VPN crea un tunnel crittografato sicuro su Internet.

#### <a name="how-do-i-create-a-site-to-site-vpn-connection"></a>Come creare una connessione VPN da sito a sito

Una VPN da sito a sito connette più utenti della rete aziendale ad Azure. Per creare una connessione da sito a sito nel portale di Azure, seguire questa procedura:

1. Creare una rete virtuale.

2. Specificare un server DNS

3. Creare la subnet del gateway.

4. Creare il gateway VPN. 

    ![](media/protect-personal-data-in-transit-encryption/vpn-step-01.png)

5. Creare il gateway di rete locale.

    ![](media/protect-personal-data-in-transit-encryption/vpn-step-02.png)

6. Configurare il dispositivo VPN.

7. Creare la connessione VPN.

    ![](media/protect-personal-data-in-transit-encryption/vpn-step-03.png)

8. Verificare la connessione VPN.

Per istruzioni più dettagliate su come creare una connessione da sito a sito nel portale di Azure, vedere [Creare una connessione da sito a sito nel portale di Azure] (https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal).

#### <a name="how-do-i-create-a-point-to-site-vpn-connection"></a>Come creare una connessione VPN da punto a sito

Una VPN da punto a sito crea una connessione sicura da un singolo computer client a una rete virtuale. È utile quando ci si vuole connettere ad Azure da una posizione remota, ad esempio da casa, un hotel o un centro conferenze. Per creare una connessione da punto a sito nel portale di Azure:

1. Creare una rete virtuale.

2. Aggiungere una subnet del gateway.

3. Specificare un server DNS (facoltativo).

4. Creare un gateway di rete virtuale.

5. Generare i certificati.

6. Aggiungere il pool di indirizzi client.

7. Caricare i dati del certificato pubblico per il certificato radice.

8. Generare e installare il pacchetto di configurazione del client VPN.

9. Installare un certificato client esportato.

10. Connettersi ad Azure.

11. Verificare la connessione.

Per istruzioni più dettagliate, vedere [Configurare una connessione da punto a sito a una rete virtuale usando l'autenticazione del certificato: portale di Azure](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal).

### <a name="ssltls"></a>SSL/TLS

È consigliabile usare sempre i protocolli SSL/TLS per lo scambio di dati tra posizioni diverse. Le organizzazioni che scelgono di usare [ExpressRoute](https://docs.microsoft.com/azure/expressroute/) per spostare set di dati di grandi dimensioni su un collegamento WAN ad alta velocità dedicato possono anche crittografare i dati a livello di applicazione usando SSL/TLS o altri protocolli per una maggiore protezione.

### <a name="encryption-by-default"></a>Crittografia predefinita

Microsoft usa la crittografia per proteggere i dati in transito tra i clienti e i servizi cloud di Azure. Se si interagisce con Archiviazione di Azure tramite il portale di Azure, tutte le transazioni hanno luogo tramite HTTPS.

[Transport Layer Security](https://en.wikipedia.org/wiki/Transport_Layer_Security) (TLS) è il protocollo che i data center Microsoft tentano di negoziare con i sistemi client che si connettono a servizi cloud Microsoft. TLS offre autenticazione avanzata, privacy e integrità dei messaggi (supportando il rilevamento di manomissioni, intercettazioni e falsificazioni di messaggi), interoperabilità, flessibilità degli algoritmi e facilità di distribuzione e uso.

Viene anche usata la funzionalità [Perfect Forward Secrecy](https://en.wikipedia.org/wiki/Forward_secrecy) (PFS) affinché ogni connessione tra i sistemi client dei clienti e i servizi cloud Microsoft usi chiavi univoche. Le connessioni ai servizi cloud Microsoft sfruttano anche chiavi di crittografia di 2.048 bit di lunghezza basate su RSA. La combinazione di TLS, chiavi RSA di 2.048 bit di lunghezza e PFS rende molto più difficile intercettare i dati in transito tra i servizi cloud Microsoft e i clienti e quindi accedervi.

I dati in transito sono sempre crittografati in [Data Lake Store] (https://docs.microsoft.com/azure/data-lake-store/data-lake-store-security-overview). I dati, oltre a essere crittografati prima dell'archiviazione in supporti persistenti, vengono sempre protetti anche mentre sono in transito usando HTTPS. HTTPS è l'unico protocollo supportato per le interfacce REST di Data Lake Store.

## <a name="summary"></a>Riepilogo

L'azienda può raggiungere il proprio obiettivo di proteggere i dati personali e la privacy di tali dati imponendo connessioni HTTPS con Archiviazione di Azure, usando firme di accesso condiviso e abilitando l'opzione Trasferimento sicuro obbligatorio negli account di archiviazione. Può anche proteggere i dati personali usando connessioni SMB 3.0 e implementando la crittografia lato client. Le connessioni VPN da sito a sito dalla rete aziendale alla rete virtuale di Azure e le connessioni VPN da punto a sito dai singoli utenti creeranno un tunnel sicuro che garantirà il trasferimento sicuro dei dati personali. La privacy dei dati personali sarà ulteriormente protetta dalle procedure di crittografia predefinite di Microsoft.

## <a name="next-steps"></a>Passaggi successivi

- [Procedure consigliate per la sicurezza dei dati e la crittografia in Azure](https://docs.microsoft.com/azure/security/azure-security-data-encryption-best-practices)

- [Pianificazione e progettazione per il gateway VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design)

- [Domande frequenti sul gateway VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-vpn-faq)

- [Acquistare e configurare un certificato SSL per il servizio app di Azure](https://docs.microsoft.com/azure/app-service/web-sites-purchase-ssl-web-site)
