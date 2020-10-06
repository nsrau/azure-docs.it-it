---
title: Diagnosticare i problemi di configurazione dei collegamenti privati nei Azure Key Vault
description: Risolvere i problemi comuni dei collegamenti privati con Key Vault e approfondire la configurazione
author: msfcolombo
ms.author: fcolombo
ms.date: 09/30/2020
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.openlocfilehash: 52ac5b89a0c7173b9b2585f84b5f34361b4b136c
ms.sourcegitcommit: a07a01afc9bffa0582519b57aa4967d27adcf91a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "91744220"
---
# <a name="diagnose-private-links-configuration-issues-on-azure-key-vault"></a>Diagnosticare i problemi di configurazione dei collegamenti privati nei Azure Key Vault

## <a name="introduction"></a>Introduzione

Questo articolo consente agli utenti di diagnosticare e risolvere i problemi che coinvolgono Key Vault e la funzionalità dei collegamenti privati. In questa guida vengono illustrati gli aspetti della configurazione, ad esempio il recupero di collegamenti privati per la prima volta o per la risoluzione di una situazione in cui i collegamenti privati hanno smesso di funzionare a causa di alcune modifiche.

Se non si ha familiarità con questa funzionalità, vedere [integrare Key Vault con il collegamento privato di Azure](private-link-service.md).

### <a name="problems-covered-by-this-article"></a>Problemi trattati in questo articolo

- Le query DNS restituiscono ancora un indirizzo IP pubblico per l'insieme di credenziali delle chiavi, anziché un indirizzo IP privato che si prevede di usare la funzionalità dei collegamenti privati.
- Tutte le richieste effettuate da un determinato client che utilizza il collegamento privato, hanno esito negativo con timeout o errori di rete e il problema non è intermittente.
- L'insieme di credenziali delle chiavi ha un indirizzo IP privato, ma le richieste ricevono comunque una `403` risposta con il `ForbiddenByFirewall` codice di errore interno.
- Si usano i collegamenti privati, ma l'insieme di credenziali delle chiavi accetta comunque le richieste provenienti dalla rete Internet pubblica.
- L'insieme di credenziali delle chiavi ha due endpoint privati. Le richieste che usano uno funzionano correttamente, ma le richieste che usano l'altra non hanno esito positivo.
- Si dispone di un'altra sottoscrizione, Key Vault o rete virtuale che utilizza collegamenti privati. Si vuole creare una nuova distribuzione simile, ma non è possibile ottenere collegamenti privati per il lavoro.

### <a name="problems-not-covered-by-this-article"></a>Problemi non trattati in questo articolo

- Si è verificato un problema di connettività intermittente. In un client specifico, vengono visualizzate alcune richieste che funzionano e alcune non funzionano. *I problemi intermittenti non sono in genere causati da un problema nella configurazione dei collegamenti privati; si tratta di un segno di sovraccarico di rete o client.*
- Si usa un prodotto Azure che supporta BYOK (Bring Your Own Key) o CMK (chiavi gestite dal cliente) e il prodotto non può accedere all'insieme di credenziali delle chiavi. *Esaminare l'altra documentazione del prodotto. Assicurarsi che specifichi in modo esplicito il supporto per gli insiemi di credenziali delle chiavi con il firewall abilitato. Se necessario, contattare il supporto tecnico per il prodotto specifico.*

### <a name="how-to-read-this-article"></a>Come leggere questo articolo

Se non si ha familiarità con i collegamenti privati o si sta valutando una distribuzione complessa, è consigliabile leggere l'intero articolo. In caso contrario, è possibile scegliere la sezione che è più sensata per il problema riscontrato.

È possibile iniziare subito.

## <a name="1-confirm-that-you-own-the-client-connection"></a>1. confermare che si è proprietari della connessione client

### <a name="confirm-that-your-client-runs-at-the-virtual-network"></a>Verificare che il client venga eseguito nella rete virtuale

Questa guida è utile per correggere le connessioni a Key Vault che provengono dal codice dell'applicazione. Esempi sono le applicazioni e gli script eseguiti in macchine virtuali di Azure, cluster di Azure Service Fabric, app Azure servizio, Azure Kubernetes Service (AKS) e altri simili.

Per definizione dei collegamenti privati, l'applicazione o lo script deve essere in esecuzione in un computer, in un cluster o in un ambiente connesso alla rete virtuale in cui è stata distribuita la [risorsa dell'endpoint privato](../../private-link/private-endpoint-overview.md) . Se l'applicazione è in esecuzione in una rete connessa a Internet arbitraria, questa guida non è applicabile e probabilmente non è possibile usare collegamenti privati.

### <a name="if-you-use-a-managed-solution-refer-to-specific-documentation"></a>Se si usa una soluzione gestita, fare riferimento alla documentazione specifica

Questa guida non è applicabile alle soluzioni gestite da Microsoft, in cui l'insieme di credenziali delle chiavi è accessibile da un prodotto Azure esistente indipendentemente dalla rete virtuale del cliente. Esempi di scenari di questo tipo sono archiviazione di Azure o Azure SQL configurato per la crittografia dei dati inattivi, l'hub eventi di Azure crittografia dei dati con chiavi fornite dal cliente, Azure Data Factory l'accesso alle credenziali del servizio archiviate in Key Vault, Azure Pipelines il recupero di segreti da Key Vault e altri scenari simili. In questi casi, *è necessario controllare se il prodotto supporta gli insiemi di credenziali delle chiavi con il firewall abilitato*. Questo supporto viene in genere eseguito con la funzionalità [Servizi attendibili](overview-vnet-service-endpoints.md#trusted-services) di Key Vault firewall. Tuttavia, molti prodotti non sono inclusi nell'elenco dei servizi attendibili per diversi motivi. In tal caso, raggiungere il supporto specifico del prodotto.

Un numero ridotto di prodotti Azure supporta il concetto di *VNET Injection*. In termini semplici, il prodotto aggiunge un dispositivo di rete alla rete virtuale del cliente, consentendo di inviare richieste come se fosse stato distribuito alla rete virtuale. Un esempio importante è [Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject). I prodotti di questo tipo possono eseguire richieste all'insieme di credenziali delle chiavi usando i collegamenti privati. questa guida alla risoluzione dei problemi può essere utile.

## <a name="2-confirm-that-the-connection-is-approved-and-succeeded"></a>2. Verificare che la connessione sia approvata e completata

I passaggi seguenti convalidano che la connessione all'endpoint privato sia approvata e riuscita:

1. Aprire il portale di Azure e aprire la risorsa di Key Vault.
2. Nel menu a sinistra selezionare **rete**.
3. Fare clic sulla scheda **connessioni endpoint private** . In questo modo vengono visualizzate tutte le connessioni agli endpoint privati e i rispettivi Stati. Se non sono presenti connessioni o se la connessione per la rete virtuale non è presente, è necessario creare un nuovo endpoint privato. Questo verrà trattato in seguito.
4. Sempre in **connessioni a endpoint privati**individuare quello da diagnosticare e verificare che "stato della connessione" sia **approvato** e che lo stato di provisioning sia **completato**.
    - Se lo stato della connessione è "in sospeso", potrebbe essere sufficiente approvarlo.
    - Se la connessione "rejected", "failed", "Error", "Disconnected" o un altro stato, non è affatto efficace, è necessario creare una nuova risorsa endpoint privata.

È consigliabile eliminare le connessioni inattive per garantire la pulizia.

## <a name="3-confirm-that-the-key-vault-firewall-is-properly-configured"></a>3. Verificare che il firewall dell'insieme di credenziali delle chiavi sia configurato correttamente

>[!IMPORTANT]
> La modifica delle impostazioni del firewall può rimuovere l'accesso da client legittimi che non utilizzano ancora collegamenti privati. Assicurarsi di conoscere le implicazioni di ogni modifica apportata alla configurazione del firewall.

Un concetto importante è che i collegamenti privati *forniscono* solo l'accesso all'insieme di credenziali delle chiavi. Non *rimuove* alcun accesso esistente. Per bloccare efficacemente gli accessi dalla rete Internet pubblica, è necessario abilitare in modo esplicito il firewall di Key Vault:

1. Aprire il portale di Azure e aprire la risorsa di Key Vault.
2. Nel menu a sinistra selezionare **rete**.
3. Verificare che nella parte superiore sia selezionata la scheda **firewall e reti virtuali** .
4. Verificare che sia selezionata l'opzione **endpoint privato e reti selezionate** . Se si seleziona **tutte le reti** , viene illustrato il motivo per cui i client esterni possono ancora accedere all'insieme di credenziali delle chiavi.

Le istruzioni seguenti si applicano anche alle impostazioni del firewall:

- La funzionalità collegamenti privati non richiede la specifica di "rete virtuale" nelle impostazioni del firewall di Key Vault. Tutte le richieste che usano l'indirizzo IP privato dell'insieme di credenziali delle chiavi (vedere la sezione successiva) devono funzionare anche se non è specificata alcuna rete virtuale nelle impostazioni del firewall di Key Vault.
- La funzionalità collegamenti privati non richiede la specifica di un indirizzo IP nelle impostazioni del firewall di Key Vault. Anche in questo caso, tutte le richieste che usano l'indirizzo IP privato dell'insieme di credenziali delle chiavi devono funzionare anche se non è stato specificato alcun indirizzo IP nelle impostazioni del firewall.

Se si usano i collegamenti privati, le uniche motivazioni per specificare la rete virtuale o l'indirizzo IP nel firewall di Key Vault sono:

- Si dispone di un sistema ibrido in cui alcuni client usano collegamenti privati, alcuni usano gli endpoint del servizio, altri usano un indirizzo IP pubblico.
- Si sta passando a collegamenti privati. In questo caso, dopo aver verificato che tutti i client usano i collegamenti privati, è necessario rimuovere le reti virtuali e gli indirizzi IP dalle impostazioni del firewall di Key Vault.

## <a name="4-make-sure-the-key-vault-has-a-private-ip-address"></a>4. Assicurarsi che l'insieme di credenziali delle chiavi disponga di un indirizzo IP privato

### <a name="difference-between-private-and-public-ip-addresses"></a>Differenza tra indirizzi IP privati e pubblici

Un indirizzo IP privato ha sempre uno dei seguenti formati:

- 10. x.x. x: esempi: `10.1.2.3` , `10.56.34.12` .
- 172.16. x.x. x a 172.32. x. x: esempi: `172.20.1.1` , `172.31.67.89` .
- 192.168. x.x. x: esempi: `192.168.0.1` , `192.168.100.7`

Alcuni indirizzi IP e intervalli sono riservati:

- 224. x.x. x: multicast
- 255.255.255.255: broadcast
- 127. x.x. x: loopback
- 169.254. x.x. x: collegamento locale
- 168.63.129.16: DNS interno

Tutti gli altri indirizzi IP sono pubblici.

Quando si Esplora il portale o si esegue un comando che mostra l'indirizzo IP, verificare che sia possibile identificare se tale indirizzo IP è privato, pubblico o riservato. Per il funzionamento dei collegamenti privati, il nome host dell'insieme di credenziali delle chiavi deve essere risolto in un indirizzo IP privato appartenente allo spazio di indirizzi della rete virtuale.

### <a name="find-the-key-vault-private-ip-address-in-the-virtual-network"></a>Trovare l'indirizzo IP privato dell'insieme di credenziali delle chiavi nella rete virtuale

È necessario diagnosticare la risoluzione del nome host e, per questo, è necessario essere a conoscenza dell'indirizzo IP privato esatto dell'insieme di credenziali delle chiavi con i collegamenti privati abilitati. Per trovare l'indirizzo, seguire questa procedura:

1. Aprire il portale di Azure e aprire la risorsa di Key Vault.
2. Nel menu a sinistra selezionare **rete**.
3. Fare clic sulla scheda **connessioni endpoint private** . In questo modo vengono visualizzate tutte le connessioni agli endpoint privati e i rispettivi Stati.
4. Trovare quello da diagnosticare e verificare che "stato della connessione" sia **approvato** e che lo stato di provisioning sia **riuscito**. Se non viene visualizzato, tornare alle sezioni precedenti di questo documento.
5. Quando si trova l'elemento corretto, fare clic sul collegamento nella colonna **endpoint privato** . Verrà aperta la risorsa endpoint privato.
6. La pagina Panoramica può visualizzare una sezione denominata **impostazioni DNS personalizzate**. Verificare che sia presente una sola voce che corrisponda al nome host dell'insieme di credenziali delle chiavi. Tale voce Mostra l'indirizzo IP privato dell'insieme di credenziali delle chiavi.
7. È anche possibile fare clic sul collegamento all' **interfaccia di rete** e verificare che l'indirizzo IP privato sia uguale a quello visualizzato nel passaggio precedente. L'interfaccia di rete è un dispositivo virtuale che rappresenta l'insieme di credenziali delle chiavi.

L'indirizzo IP è quello in cui le macchine virtuali e gli altri dispositivi *in esecuzione nella stessa rete virtuale* utilizzeranno per connettersi all'insieme di credenziali delle chiavi. Prendere nota dell'indirizzo IP oppure tenere aperta la scheda del browser e non toccarla mentre si eseguono ulteriori indagini.

>[!NOTE]
> Se l'insieme di credenziali delle chiavi ha più endpoint privati, avrà più indirizzi IP privati. Questa opzione è utile solo se si hanno più reti virtuali che accedono allo stesso insieme di credenziali delle chiavi, ciascuna tramite il proprio endpoint privato (l'endpoint privato appartiene a una singola rete virtuale). Assicurarsi di diagnosticare il problema per la rete virtuale corretta e selezionare la connessione all'endpoint privato corretta nella procedura precedente. Inoltre, **non** creare più endpoint privati per lo stesso Key Vault nella stessa rete virtuale. Questa operazione non è necessaria e costituisce una fonte di confusione.

## <a name="5-validate-the-dns-resolution"></a>5. convalidare la risoluzione DNS

La risoluzione DNS è il processo di conversione del nome host dell'insieme di credenziali delle chiavi (esempio: `fabrikam.vault.azure.net` ) in un indirizzo IP (ad esempio: `10.1.2.3` ). Nelle sottosezioni seguenti vengono illustrati i risultati previsti della risoluzione DNS in ogni scenario.

### <a name="key-vaults-without-private-link"></a>Key Vault senza collegamento privato

Questa sezione è destinata ai fini dell'apprendimento. Quando l'insieme di credenziali delle chiavi non dispone di una connessione all'endpoint privato nello stato approvato, la risoluzione del nome host restituisce un risultato simile a quello seguente:

Windows:

    C:\> nslookup fabrikam.vault.azure.net

    Non-authoritative answer:
    Address:  52.168.109.101
    Aliases:  fabrikam.vault.azure.net
              data-prod-eus.vaultcore.azure.net
              data-prod-eus-region.vaultcore.azure.net

Linux:

    joe@MyUbuntu:~$ host fabrikam.vault.azure.net

    fabrikam.vault.azure.net is an alias for data-prod-eus.vaultcore.azure.net.
    data-prod-eus.vaultcore.azure.net is an alias for data-prod-eus-region.vaultcore.azure.net.
    data-prod-eus-region.vaultcore.azure.net has address 52.168.109.101

Come si può notare, il nome viene risolto in un indirizzo IP pubblico e non esiste alcun `privatelink` alias. L'alias viene illustrato più avanti, quindi non è un problema.

Il risultato precedente è previsto indipendentemente dal fatto che il computer sia connesso alla rete virtuale o sia un computer arbitrario con una connessione Internet. Questo problema si verifica perché l'insieme di credenziali delle chiavi non dispone di una connessione all'endpoint privato nello stato approvato e pertanto non è necessario che l'insieme di credenziali delle chiavi supporti i collegamenti privati.

### <a name="key-vault-with-private-link-resolving-from-arbitrary-internet-machine"></a>Insieme di credenziali delle chiavi con la risoluzione dei collegamenti privati da un computer Internet arbitrario

Quando l'insieme di credenziali delle chiavi ha una o più connessioni a endpoint privato in stato approvato e si risolve il nome host da un computer arbitrario connesso a Internet (un computer che *non è* connesso alla rete virtuale in cui risiede l'endpoint privato), si noterà quanto segue:

Windows:

    C:\> nslookup fabrikam.vault.azure.net

    Non-authoritative answer:
    Address:  52.168.109.101
    Aliases:  fabrikam.vault.azure.net
              fabrikam.privatelink.vaultcore.azure.net
              data-prod-eus.vaultcore.azure.net
              data-prod-eus-region.vaultcore.azure.net

Linux:

    joe@MyUbuntu:~$ host fabrikam.vault.azure.net

    fabrikam.vault.azure.net is an alias for fabrikam.privatelink.vaultcore.azure.net.
    fabrikam.privatelink.vaultcore.azure.net is an alias for data-prod-eus.vaultcore.azure.net.
    data-prod-eus.vaultcore.azure.net is an alias for data-prod-eus-region.vaultcore.azure.net.
    data-prod-eus-region.vaultcore.azure.net has address 52.168.109.101

La differenza rilevante rispetto allo scenario precedente è la presenza di un nuovo alias con il valore `{vaultname}.privatelink.vaultcore.azure.net` . Questo significa che il piano dati dell'insieme di credenziali delle chiavi è pronto per accettare richieste da collegamenti privati.

Non significa che le richieste eseguite da computer *all'esterno* della rete virtuale, come quella appena usata, useranno i collegamenti privati. È possibile notare che il nome host viene ancora risolto in un indirizzo IP pubblico. Solo *i computer connessi alla rete virtuale* possono usare i collegamenti privati. Ulteriori informazioni su questa operazione seguiranno.

Se l'alias non è visibile `privatelink` , significa che l'insieme di credenziali delle chiavi ha zero connessioni di endpoint privato nello `Approved` stato. Tornare a [questa sezione](#2-confirm-that-the-connection-is-approved-and-succeeded) prima di riprovare.

### <a name="key-vault-with-private-link-resolving-from-virtual-network"></a>Insieme di credenziali delle chiavi con collegamento privato che risolve la rete virtuale

Quando l'insieme di credenziali delle chiavi ha una o più connessioni a endpoint privato in stato approvato e si risolve il nome host da un computer connesso alla rete virtuale in cui è stato creato l'endpoint privato, si tratta della risposta prevista:

Windows:

    C:\> nslookup fabrikam.vault.azure.net

    Non-authoritative answer:
    Address:  10.1.2.3
    Aliases:  fabrikam.vault.azure.net
              fabrikam.privatelink.vaultcore.azure.net

Linux:

    joe@MyUbuntu:~$ host fabrikam.vault.azure.net

    fabrikam.vault.azure.net is an alias for fabrikam.privatelink.vaultcore.azure.net.
    fabrikam.privatelink.vaultcore.azure.net has address 10.1.2.3

Esistono due differenze importanti. In primo luogo, il nome viene risolto in un indirizzo IP privato. Deve corrispondere all'indirizzo IP trovato nella [sezione corrispondente](#find-the-key-vault-private-ip-address-in-the-virtual-network) di questo articolo. In secondo luogo, non ci sono altri alias successivi a `privatelink` quello. Ciò accade perché i server DNS della rete virtuale *intercettano* la catena di alias e restituiscono l'indirizzo IP privato direttamente dal nome `fabrikam.privatelink.vaultcore.azure.net` . Tale voce è effettivamente un `A` record in una zona DNS privato. Ulteriori informazioni su questa operazione seguiranno.

>[!NOTE]
> Il risultato precedente si verifica solo in una macchina virtuale connessa alla rete virtuale in cui è stato creato l'endpoint privato. Se non è stata distribuita una macchina virtuale nella rete virtuale che contiene l'endpoint privato, distribuirne una e connettersi in modalità remota, quindi eseguire il `nslookup` comando (Windows) o il `host` comando (Linux) precedente.

Se si eseguono questi comandi in una macchina virtuale connessa alla rete virtuale in cui è stato creato l'endpoint privato e **non** viene visualizzato l'indirizzo IP privato dell'insieme di credenziali delle chiavi, la sezione successiva potrebbe contribuire a risolvere il problema.

## <a name="6-validate-the-private-dns-zone"></a>6. convalidare la zona di DNS privato

Se la risoluzione DNS non funziona come descritto nella sezione precedente, potrebbe essersi verificato un problema con la zona di DNS privato e questa sezione può essere utile. Se la risoluzione DNS Visualizza l'indirizzo IP privato dell'insieme di credenziali delle chiavi corretto, è probabile che la zona DNS privato sia corretta. È possibile ignorare questa intera sezione.

### <a name="confirm-that-the-required-private-dns-zone-resource-exists"></a>Verificare che la risorsa DNS privato zona necessaria esista

La sottoscrizione di Azure deve avere una risorsa [DNS privato zona](../../dns/private-dns-privatednszone.md) con questo nome esatto:

    privatelink.vaultcore.azure.net

È possibile verificare la presenza di questa risorsa passando alla pagina sottoscrizione nel portale e selezionando "risorse" nel menu a sinistra. Il nome della risorsa deve essere `privatelink.vaultcore.azure.net` e il tipo di risorsa deve essere **DNS privato zona**.

Normalmente questa risorsa viene creata automaticamente quando si crea un endpoint privato usando un metodo tipico. In alcuni casi, tuttavia, questa risorsa non viene creata automaticamente ed è necessario eseguire questa operazione manualmente. È possibile che la risorsa sia stata eliminata accidentalmente.

Se questa risorsa non è presente, creare una nuova risorsa DNS privato zona nella sottoscrizione. Tenere presente che il nome deve essere esattamente `privatelink.vaultcore.azure.net` , senza spazi o punti aggiuntivi. Se si specifica il nome errato, la risoluzione dei nomi descritta in questo articolo non funzionerà. Per altre informazioni su come creare questa risorsa, vedere [creare una zona DNS privata di Azure usando il portale di Azure](../../dns/private-dns-getstarted-portal.md). Se si segue questa pagina, è possibile ignorare la creazione della rete virtuale perché a questo punto è necessario avere già una. È anche possibile ignorare le procedure di convalida con le macchine virtuali.

### <a name="confirm-that-the-private-dns-zone-must-be-linked-to-the-virtual-network"></a>Confermare che la zona di DNS privato deve essere collegata alla rete virtuale

Non è sufficiente avere una zona DNS privato. Deve anche essere collegato alla rete virtuale che contiene l'endpoint privato. Se la zona di DNS privato non è collegata alla rete virtuale corretta, qualsiasi risoluzione DNS della rete virtuale ignorerà la zona DNS privato.

Aprire la risorsa zona DNS privato e fare clic sull'opzione **collegamenti rete virtuale** nel menu a sinistra. Verrà visualizzato un elenco di collegamenti, ognuno con il nome di un Rete in ingresso virtuale della sottoscrizione. La rete virtuale che contiene la risorsa endpoint privata deve essere elencata qui. Se non lo è, aggiungerla. Per i passaggi dettagliati, vedere [collegare la rete virtuale](../../dns/private-dns-getstarted-portal.md#link-the-virtual-network). È possibile lasciare deselezionata l'opzione "Abilita registrazione automatica". questa funzionalità non è correlata a collegamenti privati.

Una volta collegata la zona di DNS privato alla rete virtuale, le richieste DNS provenienti dalla rete virtuale cercheranno i nomi nella zona DNS privato. Questa operazione è necessaria per la corretta risoluzione degli indirizzi eseguita nelle macchine virtuali connesse alla rete virtuale in cui è stato creato l'endpoint privato.

>[!NOTE]
> Se è stato appena salvato il collegamento, l'esecuzione di questa operazione potrebbe richiedere alcuni minuti, anche dopo che il portale ha indicato che l'operazione è stata completata. Potrebbe anche essere necessario riavviare la macchina virtuale usata per testare la risoluzione DNS.

### <a name="confirm-that-the-private-dns-zone-contains-the-right-a-record"></a>Verificare che la zona DNS privato contenga il record A corretto

Usando il portale, aprire la zona DNS privato con nome `privatelink.vaultcore.azure.net` . Nella pagina panoramica vengono visualizzati tutti i record. Per impostazione predefinita, sarà presente un record con nome `@` e tipo `SOA` , che significa inizio dell'autorità. Non toccarlo.

Per il corretto funzionamento della risoluzione dei nomi di Key Vault, è necessario che sia presente un `A` record con il nome dell'insieme di credenziali semplice senza suffisso o punti. Se, ad esempio, il nome host è `fabrikam.vault.azure.net` , deve essere presente un `A` record con il nome `fabrikam` , senza suffissi o punti.

Inoltre, il valore del `A` record (indirizzo IP) deve essere [l'indirizzo IP privato](#find-the-key-vault-private-ip-address-in-the-virtual-network)dell'insieme di credenziali delle chiavi. Se il record viene trovato `A` ma è contenuto nell'indirizzo IP errato, è necessario rimuovere l'indirizzo IP errato e aggiungerne uno nuovo. Si consiglia di rimuovere l'intero `A` record e di aggiungerne uno nuovo.

>[!NOTE]
> Ogni volta che si rimuove o si modifica un `A` record, il computer potrebbe comunque risolversi nell'indirizzo IP precedente, perché il valore TTL (time to Live) potrebbe non essere ancora scaduto. Si consiglia di specificare sempre un valore TTL non inferiore a 60 secondi (un minuto) e non più grande di 600 secondi (10 minuti). Se si specifica un valore troppo grande, i client potrebbero richiedere troppo tempo per il ripristino in caso di interruzioni.

### <a name="dns-resolution-for-more-than-one-virtual-network"></a>Risoluzione DNS per più di una rete virtuale

Se sono presenti più reti virtuali e ognuna ha una risorsa di endpoint privato che fa riferimento allo stesso insieme di credenziali delle chiavi, il nome host dell'insieme di credenziali delle chiavi deve essere risolto in un indirizzo IP privato diverso a seconda della rete. Ciò significa che sono necessarie più zone DNS privato, ciascuna collegata a una rete virtuale diversa e usando un indirizzo IP diverso nel `A` record.

Negli scenari più avanzati sono disponibili più reti virtuali con peering abilitato. In questo caso, solo una rete virtuale necessita della risorsa endpoint privato, sebbene sia necessario che sia collegata alla risorsa DNS privato zona. Questo scenario non è coperto direttamente da questo documento.

### <a name="fact-you-have-control-over-dns-resolution"></a>Fact: si ha il controllo sulla risoluzione DNS

Come illustrato nella [sezione precedente](#key-vault-with-private-link-resolving-from-arbitrary-internet-machine), un insieme di credenziali delle chiavi con collegamenti privati ha l'alias `{vaultname}.privatelink.vaultcore.azure.net` nella registrazione *pubblica* . Il server DNS usato dalla rete virtuale usa la registrazione pubblica, ma controlla ogni alias per una registrazione *privata* . se ne viene trovato uno, arresterà gli alias seguenti definiti alla registrazione pubblica.

Questa logica significa che se la rete virtuale è collegata a una zona DNS privato con nome `privatelink.vaultcore.azure.net` e la registrazione DNS pubblica per l'insieme di credenziali delle chiavi presenta l'alias `fabrikam.privatelink.vaultcore.azure.net` (si noti che il suffisso nome host dell'insieme di credenziali delle chiavi corrisponde esattamente al nome della zona di DNS privato), la query DNS cercherà un `A` record con nome `fabrikam` *nella zona DNS privato*. Se il `A` record viene trovato, il relativo indirizzo IP viene restituito nella query DNS e non viene eseguita alcuna ricerca aggiuntiva alla registrazione DNS pubblica.

Come si può notare, la risoluzione dei nomi è sotto il proprio controllo. Le logiche per questa progettazione sono:

- È possibile che si disponga di uno scenario complesso che include server DNS personalizzati e l'integrazione con le reti locali. In tal caso, è necessario controllare il modo in cui i nomi vengono convertiti in indirizzi IP.
- Potrebbe essere necessario accedere a un insieme di credenziali delle chiavi senza collegamenti privati. In tal caso, la risoluzione del nome host dalla rete virtuale deve restituire l'indirizzo IP pubblico. questa situazione si verifica perché gli insiemi di credenziali delle chiavi senza collegamenti privati non hanno l' `privatelink` alias nella registrazione del nome.

## <a name="7-validate-that-requests-to-key-vault-use-private-link"></a>7. Verificare che le richieste a Key Vault usino il collegamento privato

### <a name="query-the-healthstatus-endpoint-of-the-key-vault"></a>Eseguire una query sull' `/healthstatus` endpoint dell'insieme di credenziali delle chiavi

L'insieme di credenziali delle chiavi fornisce l' `/healthstatus` endpoint, che può essere usato per la diagnostica. Le intestazioni di risposta includono l'indirizzo IP di origine, come visto dal servizio Key Vault. È possibile chiamare l'endpoint con il comando seguente (**ricordare di usare il nome host**dell'insieme di credenziali delle chiavi):

Windows (PowerShell):

    PS C:\> $(Invoke-WebRequest -UseBasicParsing -Uri https://fabrikam.vault.azure.net/healthstatus).Headers

    Key                           Value
    ---                           -----
    Pragma                        no-cache
    x-ms-request-id               3729ddde-eb6d-4060-af2b-aac08661d2ec
    x-ms-keyvault-service-version 1.2.27.0
    x-ms-keyvault-network-info    addr=10.4.5.6;act_addr_fam=InterNetworkV6;
    Strict-Transport-Security     max-age=31536000;includeSubDomains
    Content-Length                4
    Cache-Control                 no-cache
    Content-Type                  application/json; charset=utf-8

Linux o una versione recente di Windows 10 che include `curl` :

    joe@MyUbuntu:~$ curl -i https://fabrikam.vault.azure.net/healthstatus
    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Pragma: no-cache
    Content-Type: application/json; charset=utf-8
    x-ms-request-id: 6c090c46-0a1c-48ab-b740-3442ce17e75e
    x-ms-keyvault-service-version: 1.2.27.0
    x-ms-keyvault-network-info: addr=10.4.5.6;act_addr_fam=InterNetworkV6;
    Strict-Transport-Security: max-age=31536000;includeSubDomains
    Content-Length: 4

Se non viene restituito un output simile a quello o se si riceve un errore di rete, significa che l'insieme di credenziali delle chiavi non è accessibile tramite il nome host specificato ( `fabrikam.vault.azure.net` nell'esempio). Il nome host non viene risolto nell'indirizzo IP corretto oppure si verifica un problema di connettività a livello di trasporto. Il problema potrebbe essere causato da problemi di routing, cadute di pacchetti e altri motivi. È necessario approfondire l'analisi.

La risposta deve includere l'intestazione `x-ms-keyvault-network-info` :

    x-ms-keyvault-network-info: addr=10.4.5.6;act_addr_fam=InterNetworkV6;

Il `addr` campo nell' `x-ms-keyvault-network-info` intestazione Mostra l'indirizzo IP dell'origine della richiesta. Questo indirizzo IP può essere uno dei seguenti:

- Indirizzo IP privato del computer che esegue la richiesta. Ciò indica che la richiesta usa i collegamenti privati o gli endpoint di servizio. Questo è il risultato previsto per i collegamenti privati.
- Un altro indirizzo IP privato, *non* dal computer che esegue la richiesta. Ciò indica che il routing personalizzato è efficace. Indica comunque che la richiesta usa i collegamenti privati o gli endpoint di servizio.
- Un indirizzo IP pubblico. Ciò indica che la richiesta viene instradata a Internet tramite un dispositivo gateway (NAT). Ciò indica che la richiesta non usa collegamenti privati e che è necessario risolvere alcuni problemi. I motivi comuni per questo problema sono 1) l'endpoint privato non è in stato approvato e riuscito. e 2) il nome host non viene risolto nell'indirizzo IP privato dell'insieme di credenziali delle chiavi. Questo articolo include le azioni di risoluzione dei problemi per entrambi i casi.

>[!NOTE]
> Se la richiesta `/healthstatus` funziona, ma l' `x-ms-keyvault-network-info` intestazione non è presente, è probabile che l'endpoint non venga servito dall'insieme di credenziali delle chiavi. Poiché i comandi precedenti convalidano anche il certificato HTTPS, l'intestazione mancante potrebbe essere un segno di manomissione.

### <a name="query-the-key-vault-ip-address-directly"></a>Eseguire query direttamente sull'indirizzo IP dell'insieme di credenziali delle chiavi

>[!IMPORTANT]
> L'accesso all'insieme di credenziali delle chiavi senza la convalida del certificato HTTPS è pericoloso e può essere usato solo a scopo di apprendimento. Il codice di produzione non deve mai accedere all'insieme di credenziali delle chiavi senza questa convalida lato client. Anche in caso di problemi di diagnostica, potrebbe essere soggetto a un tentativo di manomissione in corso che non verrà visualizzato se si disabilita sempre la convalida del certificato HTTPS nelle richieste a Key Vault.

Se è stata installata una versione recente di PowerShell, è possibile usare `-SkipCertificateCheck` per ignorare i controlli dei certificati HTTPS, quindi è possibile indirizzare direttamente l' [indirizzo IP](#find-the-key-vault-private-ip-address-in-the-virtual-network) dell'insieme di credenziali delle chiavi:

    PS C:\> $(Invoke-WebRequest -SkipCertificateCheck -Uri https://10.1.2.3/healthstatus).Headers

Se si usa `curl` , è possibile eseguire la stessa operazione con l' `-k` argomento:

    joe@MyUbuntu:~$ curl -i -k https://10.1.2.3/healthstatus

Le risposte devono essere uguali a quelle della sezione precedente, ovvero devono includere l' `x-ms-keyvault-network-info` intestazione con lo stesso valore. L' `/healthstatus` endpoint non è rilevante se si usa il nome host o l'indirizzo IP dell'insieme di credenziali delle chiavi.

Se viene `x-ms-keyvault-network-info` restituito un valore per la richiesta usando il nome host dell'insieme di credenziali delle chiavi e un altro valore per la richiesta che usa l'indirizzo IP, ogni richiesta è destinata a un endpoint diverso. Vedere la spiegazione del `addr` campo `x-ms-keyvault-network-info` nella sezione precedente, per decidere quale caso è errato e deve essere corretto.

## <a name="8-other-changes-and-customizations-that-cause-impact"></a>8. altre modifiche e personalizzazioni che provocano un effetto

Gli elementi seguenti sono azioni di analisi non esaustive. Verranno indicati i casi in cui cercare ulteriori problemi, ma è necessario disporre di una conoscenza avanzata della rete per risolvere i problemi in questi scenari.

### <a name="diagnose-custom-dns-servers-at-virtual-network"></a>Diagnosticare i server DNS personalizzati nella rete virtuale

Nel portale aprire la risorsa rete virtuale. Nel menu a sinistra aprire **server DNS**. Se si usa "Custom", la risoluzione DNS potrebbe non essere come descritto in questo documento. È necessario diagnosticare il modo in cui i server DNS stanno risolvendo il nome host dell'insieme di credenziali delle chiavi.

Se si usano i server DNS forniti da Azure predefiniti, l'intero documento è applicabile.

### <a name="diagnose-hosts-overriding-or-custom-dns-servers-at-virtual-machine"></a>Diagnosticare gli host che eseguono l'override o i server DNS personalizzati nella macchina virtuale

Molti sistemi operativi consentono di impostare un indirizzo IP fisso esplicito per hostname, in genere cambiando il `hosts` file. Possono inoltre consentire l'override dei server DNS. Se si usa uno di questi scenari, procedere con le opzioni di diagnostica specifiche del sistema.

### <a name="promiscuous-proxies-fiddler-etc"></a>Proxy promiscui (Fiddler e così via)

Ad eccezione di quanto specificato in modo esplicito, le opzioni di diagnostica in questo articolo funzionano solo se non sono presenti proxy promiscui nell'ambiente. Sebbene questi proxy siano spesso installati esclusivamente nel computer che viene diagnosticato (Fiddler è l'esempio più comune), gli amministratori avanzati possono sovrascrivere le autorità di certificazione radice e installare un proxy promiscuo nei dispositivi gateway che gestiscono più computer nella rete. Questi proxy possono influenzare in modo sostanziale la sicurezza e l'affidabilità. Microsoft non supporta le configurazioni che utilizzano tali prodotti.

### <a name="other-things-that-may-affect-connectivity"></a>Altre operazioni che possono influire sulla connettività

Si tratta di un elenco non esaustivo di elementi che è possibile trovare in scenari avanzati o complessi:

- Impostazioni del firewall, ovvero il firewall di Azure connesso alla rete virtuale o una soluzione firewall personalizzata distribuita nella rete virtuale o nel computer.
- Peering di rete, che può influisca sui server DNS usati e sul modo in cui viene indirizzato il traffico.
- Soluzioni per gateway personalizzati (NAT), che possono influisca sul modo in cui viene indirizzato il traffico, incluso il traffico dalle query DNS.
