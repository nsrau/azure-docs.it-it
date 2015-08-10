<properties
	pageTitle="Considerazioni sulla sicurezza per Gestione risorse di Azure"
	description="Mostra approcci consigliati di Gestione risorse di Azure per la protezione delle risorse con chiavi e segreti, il controllo degli accessi in base al ruolo e i gruppi di sicurezza di rete."
	services="azure-resource-manager"
	documentationCenter=""
	authors="george-moore"
	manager="georgem"
	editor="tysonn"/>

<tags
	ms.service="azure-resource-manager"
	ms.workload="multiple"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/24/2015"
	ms.author="georgem"/>


# Considerazioni sulla sicurezza per Gestione risorse di Azure

Osservando gli aspetti della sicurezza per i modelli di Gestione risorse di Azure, sono diverse le aree da considerare: le chiavi e i segreti, il controllo degli accessi in base al ruolo e i gruppi di sicurezza di rete.

In questo argomento si presuppone che si abbia familiarità con il controllo degli accessi in base al ruolo (RBAC) di Gestione risorse di Azure. Per ulteriori informazioni, vedere [Controllo degli accessi in base al ruolo nel portale di Microsoft Azure](role-based-access-control-configure.md) e [Gestione e controllo dell'accesso alle risorse](resource-group-rbac.md)

## Segreti e certificati

Macchine virtuali di Azure, Gestione risorse di Azure e Insieme di credenziali chiave di Azure sono completamente integrati per fornire supporto per la gestione protetta dei certificati che devono essere distribuiti nella macchina virtuale. L’utilizzo di Insieme di credenziali chiave con Gestione risorse di Azure per orchestrare e archiviare i certificati e i segreti della macchina virtuale è una procedura consigliata che offre i vantaggi seguenti:

- I modelli contengono solo i riferimenti URI ai segreti, ovvero i segreti effettivi non sono presenti nel repository di codice sorgente, configurazione o codice. In tal modo si impediscono gli attacchi di phishing chiave nei repository interni o esterni, ad esempio harvest-bot in GitHub.
- I segreti memorizzati nell'insieme di credenziali chiave sono sotto il controllo RBAC completo di un operatore attendibile. Se l'operatore attendibile lascia la società o si trasferisce in un nuovo gruppo della società, non potrà più accedere alle chiavi che ha creato nell'insieme di credenziali.
- Contesto completo di tutti gli asset:
      - Modelli per distribuire le chiavi 
      - Modelli per distribuire una macchina virtuale con riferimenti alle chiavi 
      - Materiale chiave effettive nell'insieme di credenziali. Ogni modello, e l'azione, può essere in diversi ruoli RBAC per la separazione completa dei compiti.
- Il caricamento dei segreti in una macchina virtuale in fase di distribuzione avviene tramite un canale diretto tra l'infrastruttura di Azure e l'insieme di credenziali chiave entro i confini del data center Microsoft. Una volta che le chiavi sono nell'insieme di credenziali chiave, non vedono mai 'luce' su un canale non attendibile all'esterno del data center.  
- Gli insiemi di credenziali chiave sono sempre regionali e pertanto hanno sempre la località (e la sovranità) con le macchine virtuali. Non esistono insiemi di credenziali chiave globali.

### Separazione delle chiavi dalle distribuzioni

È procedura consigliata mantenere separati i modelli per:

1.	La creazione di insiemi di credenziali (contenenti il materiale della chiave)
2.	La distribuzione delle macchine virtuali (con i riferimenti URI alle chiavi contenute negli insiemi di credenziali)

In uno scenario aziendale tipico si dispone di un piccolo gruppo di operatori affidabili che hanno accesso a segreti critici all'interno dei carichi di lavoro distribuiti, con un gruppo più ampio del personale di sviluppo e degli operatori che può creare o aggiornare le distribuzioni VM. Di seguito è riportato un modello di esempio ARM che crea e configura un nuovo insieme di credenziali nel contesto dell'identità dell'utente attualmente autenticato in Azure Active Directory. Questo utente dispone dell'autorizzazione predefinita per creare, eliminare, elencare, aggiornare, eseguire il backup, ripristinare e ottenere la metà pubblica delle chiavi in questo nuovo insieme di credenziali chiave.

Sebbene la maggior parte dei campi di questo modello sia facilmente comprensibile, l’impostazione di **enableVaultForDeployment** merita ulteriori informazioni di base: gli insiemi di credenziali non hanno un accesso in esecuzione predefinito da altri componenti dell'infrastruttura di Azure. Impostando questo valore, si consente l’accesso in lettura dei componenti dell’infrastruttura di calcolo di Azure allo specifico insieme di credenziali denominato. Pertanto, un’altra procedura consigliata consiste nel non mescolare dati aziendali riservati nello stesso insieme di credenziali dei segreti della macchina virtuale.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "keyVaultName": {
                "type": "string",
                "metadata": {
                    "description": "Name of the Vault"
                }
            },
            "location": {
                "type": "string",
                "allowedValues": ["East US", "West US", "West Europe", "East Asia", "South East Asia"],
                "metadata": {
                    "description": "Location of the Vault"
                }
            },
            "tenantId": {
                "type": "string",
                "metadata": {
                    "description": "Tenant Id of the subscription. Get using Get-AzureSubscription cmdlet or Get Subscription API"
                }
            },
            "objectId": {
                "type": "string",
                "metadata": {
                    "description": "Object Id of the AD user. Get using Get-AzureADUser cmdlet"
                }
            },
            "skuName": {
                "type": "string",
                "allowedValues": ["Standard", "Premium"],
                "metadata": {
                    "description": "SKU for the vault"
                }
            },
            "enableVaultForDeployment": {
                "type": "bool",
                "allowedValues": [true, false],
                "metadata": {
                    "description": "Specifies if the vault is enabled for a VM deployment"
                }
            }
        },
        "resources": [{
            "type": "Microsoft.KeyVault/vaults",
            "name": "[parameters('keyVaultName')]",
            "apiVersion": "2014-12-19-preview",
            "location": "[parameters('location')]",
            "properties": {
                "enabledForDeployment": "[parameters('enableVaultForDeployment')]",
                "tenantid": "[parameters('tenantId')]",
                "accessPolicies": [{
                    "tenantId": "[parameters('tenantId')]",
                    "objectId": "[parameters('objectId')]",
                    "permissions": {
                        "secrets": ["all"],
                        "keys": ["all"]
                    }
                }],
                "sku": {
                    "name": "[parameters('skuName')]",
                    "family": "A"
                }
            }
        }]
    }

Una volta creato l'insieme di credenziali, il passaggio successivo consiste nel creare il riferimento a tale insieme di credenziali nel modello di distribuzione di una nuova macchina virtuale. Come indicato in precedenza, è consigliabile disporre di un gruppo di sviluppo e degli operatori diverso che gestisce le distribuzioni di macchine virtuali, con il gruppo privo di accesso diretto alle chiavi archiviate nell'insieme di credenziali.

Il seguente frammento di modello viene composto in costrutti di distribuzione ordine superiori, ogni con riferimenti sicuri e protetti ai segreti altamente sensibili che non sono sotto il controllo diretto dell'operatore.

    "vaultName": {
        "type": "string",
        "metadata": {
            "description": "Name of Key Vault that has a secret"
        }
    },
    {
        "apiVersion": "2015-05-01-preview",
        "type": "Microsoft.Compute/virtualMachines",
        "name": "[parameters('vmName')]",
        "location": "[parameters('location')]",
        "properties": {
            "osProfile": {
                "secrets": [{
                    "sourceVault": {
                        "id": "[resourceId('vaultrg', 'Microsoft.KeyVault/vaults', 'kayvault')]"
                    },
                    "vaultCertificates": [{
                        "certificateUrl": "[parameters('secretUrlWithVersion')]",
                        "certificateStore": "My"
                    }]
                }]
            }
        }
    }

## Entità servizio per le interazioni tra sottoscrizioni

Le identità di servizio sono rappresentate dalle entità di servizio di Active Directory. Le entità servizio costituiscono l'aspetto centrale dell'abilitazione degli scenari principali per organizzazioni IT di classe enterprise, integratori di sistemi e fornitori di servizi cloud. In particolare, ci sono casi di utilizzo in cui una di queste organizzazioni deve interagire con la sottoscrizione di uno dei clienti.

L'organizzazione può fornire un'offerta che dovrà monitorare una soluzione distribuita nell’ambiente e nella sottoscrizione dei clienti. In questo caso, sarà necessario ottenere l'accesso ai registri e altri dati all'interno di un account del cliente in modo da poterlo utilizzare nella soluzione di monitoraggio. Nel caso di un'azienda IT o di un integratore di sistemi, è possibile presentare al cliente un'offerta per distribuire e gestire una capacità per suo conto, ad esempio una piattaforma di analisi dei dati, in cui l'offerta si trova nella sottoscrizione del cliente.

In questi casi di utilizzo, l'organizzazione richiede un'identità a cui può essere concesso l'accesso per eseguire queste azioni all'interno del contesto di una sottoscrizione del cliente.

Questi scenari comportano una determinata serie di considerazioni per il cliente:

-	Per motivi di sicurezza, potrebbe essere necessario l'accesso all'ambito di determinati tipi di azioni, ad esempio, l’accesso in sola lettura.
-	Quando le risorse distribuite vengono fornite a un costo, potrebbero esserci vincoli simili all'accesso necessario per motivi finanziari.
-	Per motivi di sicurezza, l'accesso potrebbe essere limitato solo a una risorsa specifica (account di archiviazione) o alle risorse (gruppo di risorse contenente un ambiente o una soluzione)
-	Poiché la relazione con un fornitore può cambiare, il cliente desidera avere la possibilità di abilitare o disabilitare l'accesso a SI o CSV
-	Poiché le azioni su questo account hanno costi di fatturazione, il cliente richiede supporto per il controllo e la responsabilità della fatturazione.
-	Dal punto di vista della conformità, il cliente desidera essere in grado di controllare il comportamento all'interno del proprio ambiente

Una combinazione di entità servizio e RBAC consente di soddisfare questi requisiti.

## Gruppi di sicurezza di rete

Molti scenari dispongono dei requisiti che specificano la modalità di controllo del traffico a una o più istanze di macchina virtuale nella rete virtuale. A tale scopo, è possibile usare un gruppo di sicurezza di rete come parte di una distribuzione di modello di Gestione risorse di Azure.

Un gruppo di sicurezza di rete è un oggetto di primo livello associato alla sottoscrizione. Un NSG contiene le regole di controllo dell’accesso che consentono o negano il traffico alle istanze di macchina virtuale. Le regole di un gruppo di sicurezza di rete possono essere modificate in qualsiasi momento e le modifiche vengono applicate a tutte le istanze associate. Per usare un gruppo di sicurezza di rete, è necessario disporre di una rete virtuale associata a una regione (località). I gruppi di sicurezza di rete non sono compatibili con le reti virtuali associate a un gruppo di affinità. Se non si dispone di una rete virtuale regionale e si vuole controllare il traffico verso gli endpoint, vedere [Informazioni sugli elenchi di controllo di accesso di rete (ACL)](https://msdn.microsoft.com/library/azure/dn376541.aspx).

È possibile associare un gruppo di sicurezza di rete a una macchina virtuale o a una subnet all'interno di una rete virtuale. Se è associato a una macchina virtuale, il gruppo di sicurezza di rete si applica a tutto il traffico inviato e ricevuto dall'istanza della macchina virtuale. Se è associato a una subnet della rete virtuale, si applica a tutto il traffico inviato e ricevuto da tutte le istanze di macchina virtuale della subnet. Una macchina virtuale o una subnet può essere associata a un solo gruppo di sicurezza di rete, ognuno dei quali può contenere un massimo di 200 regole. È possibile disporre di 100 gruppi di sicurezza di rete per sottoscrizione.

>[AZURE.NOTE]Gli elenchi di controllo di accesso basati su endpoint e i gruppi di sicurezza di rete non sono supportati nella stessa istanza di macchina virtuale. Se si vuole usare un gruppo di sicurezza di rete ed è già presente un elenco di controllo di accesso basato su endpoint, rimuovere prima l'elenco di controllo di accesso. Per informazioni su come procedere, vedere [Gestione degli elenchi di controllo di accesso (ACL) per gli endpoint tramite PowerShell](https://msdn.microsoft.com/library/azure/dn376543.aspx).

### Come funzionano i gruppi di sicurezza di rete

I gruppi di sicurezza di rete sono diversi dagli elenchi di controllo di accesso basati su endpoint. Gli elenchi di controllo di accesso basati su endpoint funzionano solo sulla porta pubblica esposta tramite l'endpoint di input. Un gruppo di sicurezza di rete funziona su una o più istanze di macchina virtuale e controlla tutto il traffico in ingresso e in uscita nella macchina virtuale.

Un gruppo di sicurezza di rete dispone di un *nome*, è associato a una *regione* (una delle località supportate da Azure) e ha un'etichetta descrittiva. Contiene due tipi di regole, In ingresso e In uscita. Le regole In ingresso vengono applicate ai pacchetti in ingresso in una macchina virtuale, mentre le regole In uscita vengono applicate ai pacchetti in uscita dalla macchina virtuale. Le regole vengono applicate al computer server in cui si trova la macchina virtuale. Un pacchetto in ingresso o in uscita deve corrispondere a una regola di assenso per essere autorizzato; in caso contrario, viene eliminato.

Le regole vengono elaborate nell'ordine di priorità. Ad esempio, una regola con un numero di priorità più basso (ad esempio 100) viene elaborata prima delle regole con numeri di priorità più alti (ad esempio 200). Dopo che viene trovata una corrispondenza, non vengono elaborate altre regole.

Una regola specifica quanto segue:

-	Nome: identificatore univoco per la regola
-	Tipo: In ingresso/In uscita
-	Priorità: numero intero compreso tra 100 e 4096 (le regole vengono elaborate dal numero più basso a quello più alto)
-	Indirizzo IP di origine: CIDR dell'intervallo di indirizzi IP di origine
-	Intervallo di porte di origine: numero intero o intervallo compreso tra 0 e 65536
-	Intervallo IP di destinazione: CIDR dell'intervallo di indirizzi IP di destinazione
-	Intervallo di porte di destinazione: numero intero o intervallo compreso tra 0 e 65536
-	Protocollo: TCP, UDP o ' \*'
-	Accesso: Consenti/Nega

### Regole predefinite

Un gruppo di sicurezza di rete contiene regole predefinite. Le regole predefinite non possono essere eliminate, ma poiché hanno la priorità più bassa, è possibile eseguirne l'override con le regole create dall'utente. Le regole predefinite descrivono le impostazioni consigliate dalla piattaforma. Come illustrato dalle regole predefinite seguenti, il traffico che origina e termina in una rete virtuale è consentito sia in ingresso che in uscita.

Mentre la connettività a Internet è consentita per la direzione in uscita, per impostazione predefinita è bloccata per la direzione in ingresso. Una regola predefinita consente il bilanciamento del carico di Azure per verificare l’integrità di una macchina virtuale. È possibile eseguire l'override di questa regola se la macchina virtuale o il set di macchine virtuali del gruppo di sicurezza di rete non fa parte del set con carico bilanciato.

Nelle tabelle seguenti vengono riportate le regole predefinite.

**Regole predefinite In ingresso**

Nome |	Priorità |	IP di origine |	Porta di origine |	IP di destinazione |	Porta di destinazione |	Protocollo |	Accesso
--- | --- | --- | --- | --- | --- | --- | ---
CONSENTI RETE VIRTUALE IN INGRESSO | 65000 | VIRTUAL\_NETWORK |	\* |	VIRTUAL\_NETWORK | \* |	\* | CONSENTI
CONSENTI BILANCIAMENTO DEL CARICO DI AZURE IN INGRESSO | 65001 | AZURE\_LOADBALANCER | \* | \* | \* | \* | CONSENTI
NEGA TUTTO IN INGRESSO | 65500 | \* | \* | \* | \* | \* | NEGA

**Regole predefinite In uscita**

Nome |	Priorità |	IP di origine |	Porta di origine |	IP di destinazione |	Porta di destinazione |	Protocollo |	Accesso
--- | --- | --- | --- | --- | --- | --- | ---
CONSENTI RETE VIRTUALE IN USCITA | 65000 | VIRTUAL\_NETWORK | \* | VIRTUAL\_NETWORK | \* | \* | CONSENTI
CONSENTI INTERNET IN USCITA | 65001 | \* | \* | INTERNET | \* | \* | CONSENTI
NEGA TUTTO IN USCITA | 65500 | \* | \* | \* | \* | \* | NEGA

### Regole speciali per l'infrastruttura

Le regole per il gruppo di sicurezza di rete sono esplicite. Non viene consentito né negato traffico oltre a quanto specificato nelle regole del gruppo di sicurezza di rete. Esistono tuttavia due tipi di traffico che vengono sempre consentiti, indipendentemente dalla specifica del gruppo di sicurezza di rete. Si tratta di regole previste a supporto dell'infrastruttura:

- **IP virtuale del nodo host**: i servizi di infrastruttura di base come DHCP, DNS e il monitoraggio dello stato vengono forniti mediante l'indirizzo IP dell'host virtualizzato 168.63.129.16. Questo indirizzo IP pubblico appartiene a Microsoft e sarà l'unico indirizzo IP virtualizzato usato in tutte le aree a questo scopo. Questo indirizzo IP è mappato all'indirizzo IP fisico del computer server (nodo host) che ospita la macchina virtuale. Il nodo host svolge la funzione di inoltro DHCP, di resolver ricorsivo DNS e di origine probe per il probe di integrità del bilanciamento del carico e per il probe di integrità del computer. La comunicazione con questo indirizzo IP non deve essere considerata come un attacco.
- **Licenze (servizio di gestione delle chiavi)**: le immagini Windows in esecuzione sulle macchine virtuali devono essere concesse in licenza. A tale scopo viene inviata una richiesta di licenza ai server host del servizio di gestione delle chiavi che gestiscono le query di questo tipo. Questo avverrà sempre sulla porta in uscita 1688.

### Tag predefiniti

I tag predefiniti sono identificatori forniti dal sistema per risolvere una categoria di indirizzi IP. I tag predefiniti possono essere specificati in regole definite dall’utente.

**Tag predefiniti per NSG**

Tag |	Descrizione
--- | ---
VIRTUAL\_NETWORK |	Indica tutto lo spazio degli indirizzi di rete. Include lo spazio di indirizzi della rete virtuale (CIDR di IP in Azure), nonché tutto lo spazio di indirizzi locale connesso (reti locali). Include anche gli spazi degli indirizzi da rete virtuale a rete virtuale.
AZURE\_LOADBALANCER | Indica il bilanciamento del carico dell'infrastruttura di Azure e viene convertito in un IP del data center di Azure da cui avranno origine i probe di integrità di Azure. È necessario solo se la macchina virtuale o il set di macchine virtuali associato al gruppo di sicurezza di rete fa parte di un set con carico bilanciato.
INTERNET | Indica lo spazio degli indirizzi IP esterno alla rete virtuale e raggiungibile tramite la rete Internet pubblica. Questo intervallo include anche lo spazio di IP pubblici appartenenti ad Azure.

### Porte e intervalli di porte

Le regole del gruppo di sicurezza di rete possono essere specificate su un'unica porta di origine/destinazione o su un intervallo di porte. Questo approccio è particolarmente utile se si vuole aprire un ampio intervallo di porte per un'applicazione, ad esempio FTP. L'intervallo deve essere sequenziale e non può essere combinato con una specifica di porta singola. Per specificare un intervallo di porte, utilizzare il segno meno (–). Ad esempio, **100-500**.

### Traffico ICMP

Con le regole NSG correnti, è possibile specificare come protocolli TCP o UDP, ma non ICMP. Il traffico ICMP è tuttavia consentito in una rete virtuale per impostazione predefinita tramite le regole In ingresso che consentono il traffico da/verso qualsiasi porta e protocollo ("\*") all'interno della rete virtuale.

### Associazione di un gruppo di sicurezza di rete a una macchina virtuale

Quando un gruppo di sicurezza di rete viene associato direttamente a una macchina virtuale, le regole di accesso alla rete nel gruppo di sicurezza di rete vengono applicate direttamente a tutto il traffico destinato alla macchina virtuale. Ogni volta che il gruppo di sicurezza di rete viene aggiornato per modifiche alle regole, la gestione del traffico riflette gli aggiornamenti in pochi minuti. Quando il gruppo di sicurezza di rete viene dissociato dalla macchina virtuale, lo stato viene ripristinato allo stato di pre-gruppo di sicurezza di rete, che rappresenta le impostazioni predefinite di sistema prima dell’introduzione del gruppo di sicurezza di rete.

### Associazione di un gruppo di sicurezza di rete a una subnet

Quando un gruppo di sicurezza di rete è associato a una subnet, le regole di accesso alla rete del gruppo di sicurezza di rete vengono applicate a tutte le macchine virtuali nella subnet. Se le regole di accesso del gruppo di sicurezza di rete vengono aggiornate, le modifiche vengono applicate a tutte le macchine virtuali nella subnet in pochi minuti.

### Associazione di un gruppo di sicurezza di rete a una subnet e una macchina virtuale

È possibile associare un gruppo di sicurezza di rete a una macchina virtuale e un altro gruppo di sicurezza di rete alla subnet in cui risiede la macchina virtuale. Questo scenario è supportato per fornire alla macchina virtuale due livelli di protezione. Sul traffico in ingresso, il pacchetto segue le regole di accesso specificate nella subnet, seguite dalle regole della macchina virtuale. Quando in uscita, il pacchetto segue prima le regole specificate nella macchina virtuale, quindi segue le regole specificate nella subnet, come illustrato di seguito.

![Associazione di un gruppo di sicurezza di rete a una subnet e una macchina virtuale](./media/best-practices-resource-manager-security/nsg-subnet-vm.png)

Quando un gruppo di sicurezza di rete viene associato a una macchina virtuale o una subnet, le regole di controllo di accesso alla rete diventano molto esplicite. La piattaforma non inserisce alcuna regola implicita per consentire il traffico su una porta specifica. In questo caso, se si crea un endpoint nella macchina virtuale, è necessario creare anche una regola per consentire il traffico da Internet. In caso contrario, il *VIP: {Port}* non è accessibile dall'esterno.

Ad esempio, è possibile creare una nuova macchina virtuale e un nuovo gruppo di sicurezza di rete. Associare il gruppo di sicurezza di rete alla macchina virtuale. La macchina virtuale può comunicare con altre macchine virtuali nella rete virtuale tramite la regola CONSENTI RETE VIRTUALE IN INGRESSO. La macchina virtuale può anche effettuare connessioni in uscita a Internet usando la regola CONSENTI INTERNET IN USCITA. In seguito si crea un endpoint sulla porta 80 per ricevere il traffico destinato al sito Web in esecuzione nella macchina virtuale. I pacchetti destinati alla porta 80 all'indirizzo VIP (indirizzo IP virtuale pubblico) da Internet non raggiungono la macchina virtuale finché non si aggiunge al gruppo di sicurezza di rete una regola simile alla tabella seguente.

**Regola esplicita che consente il traffico a una porta specifica**

Nome |	Priorità |	IP di origine |	Porta di origine |	IP di destinazione |	Porta di destinazione |	Protocollo |	Accesso
--- | --- | --- | --- | --- | --- | --- | ---
WEB | 100 | INTERNET | \* | \* | 80 | TCP | CONSENTI

## Route definite dall'utente

Azure utilizza una tabella di route per decidere come inoltrare traffico IP in base alla destinazione di ogni pacchetto. Sebbene Azure offra una tabella di route predefinita in base alle impostazioni di rete virtuale, potrebbe essere necessario aggiungere route personalizzati a tale tabella.

I più comuni necessari per una voce nella tabella della route personalizzata sono l'utilizzo di un dispositivo virtuale nell'ambiente Azure. Prendere in considerazione lo scenario illustrato nella figura riportata di seguito. Si supponga che si desidera garantire che tutto il traffico diretto a livello intermedio e sottoposti a subnet avviate dalla subnet front-end passano attraverso un accessorio virtuale firewall. Semplicemente aggiungendo l'accessorio alla rete virtuale e la connessione a subnet diverse non fornisce questa funzionalità. È inoltre necessario modificare la tabella di routing applicata alla subnet per garantire che i pacchetti vengono inoltrati al dispositivo virtuale firewall.

Lo stesso sarà true se è stato implementato un dispositivo NAT virtuale per controllare il traffico tra la rete virtuale di Azure e Internet. Per assicurarsi di utilizzata il dispositivo virtuale, è necessario creare una route specifica che tutto il traffico destinato a Internet deve essere inoltrato per il dispositivo virtuale.

### Routing.

I pacchetti vengono inoltrati attraverso una rete TCP/IP basata su una tabella di route definita in ogni nodo nella rete fisica. Una tabella di route è un insieme di route singoli utilizzata per decidere dove inoltrare pacchetti in base a indirizzo IP di destinazione. Una route è costituita dai seguenti elementi:

- Prefisso dell'indirizzo IP. CIDR di destinazione a cui viene applicata la route, ad esempio 10.1.0.0/16.
- Tipo hop successivo. Il tipo di hop Azure il pacchetto deve essere inviato. I valori possibili sono:
  - Local. Rappresenta la rete virtuale locale. Ad esempio, se sono presenti due subnet, 10.1.0.0/16 e 10.2.0.0/16 nella stessa rete virtuale, la route per ogni subnet nella tabella della route avrà un valore di hop successivo di Local.
  - Gateway VPN. Rappresenta un Gateway VPN S2S Azure.
  - Internet. Rappresenta il gateway Internet predefinito fornito dall'infrastruttura di Azure
  - Dispositivo virtuale. Rappresenta un dispositivo virtuale che aggiunto alla rete virtuale Azure.
  - NULL. Rappresenta un black hole. I pacchetti inoltrati a un black hole non verranno inoltrati affatto.
-	Valore Nexthop. Il valore di hop successivo contiene l'indirizzo IP per inoltrare i pacchetti. I valori di hop successivo sono consentiti solo nelle route dove il tipo di hop successivo è *dispositivo virtuale*. L'hop successivo deve trovarsi nella subnet (interfaccia locale del dispositivo virtuale in base all'ID di rete) e non in una subnet remota. 

![Routing.](./media/best-practices-resource-manager-security/routing.png)

### Route predefinite

Ogni subnet creata in una rete virtuale viene associata automaticamente a una tabella di route che contiene le seguenti regole di route predefinite:

- Regola di rete virtuale locale: questa regola viene creata automaticamente per ogni subnet in una rete virtuale. Specifica che vi è un collegamento diretto tra le macchine virtuali nella rete virtuale e che non esiste alcun hop successivo intermediario. Questo consente alle macchine virtuali nella stessa subnet, indipendentemente dall'ID di rete delle macchine virtuali, di comunicare tra loro senza richiedere l'uso di un indirizzo gateway predefinito.
- Regola Locale: questa regola si applica a tutto il traffico destinato ad un intervallo di indirizzi locali e utilizza il gateway VPN come destinazione hop successiva.
- Regola Internet: questa regola gestisce tutto il traffico destinato all’Internet pubblico e utilizza il gateway internet di infrastruttura come hop successivo per tutto il traffico destinato a Internet.

### Route BGP

Al momento della stesura di questo articolo, [ExpressRoute](expressroute/expressroute-introduction.md) non è ancora supportato nel [provider di risorse di rete](virtual-network/resource-groups-networking.md) per Gestione risorse di Azure. Se si dispone di una connessione ExpressRoute tra la rete locale e Azure, è possibile abilitare BGP a propagare le route dalla rete locale in Azure una volta che ExpressRoute è supportato dal provider di risorse di rete. Queste route BGP vengono utilizzate nello stesso modo come route predefinite e le route definite dall'utente in ogni subnet di Azure. Per ulteriori informazioni vedere [ExpressRoute Introduzione](expressroute/expressroute-introduction.md).

>[AZURE.NOTE]Quando ExpressRoute sarà supportato dal provider di risorse di rete, sarà possibile configurare l'ambiente Azure per usare il tunneling forzato attraverso la rete locale tramite la creazione di una route definita dall’utente per la subnet 0.0.0.0/0 che usa il gateway VPN come hop successivo. Tuttavia, funziona solo se si utilizza un gateway VPN, non ExpressRoute. Per ExpressRoute, il tunneling forzato viene configurato tramite BGP.

### Route definite dall'utente

Non è possibile visualizzare le route predefinite specificate in precedenza nell'ambiente Azure e per la maggior parte degli ambienti, questi sono solo le route che è necessario. Tuttavia, è necessario creare una tabella di route e aggiungere una o più route in casi specifici, ad esempio:

-	Tunneling forzato a Internet tramite la rete locale.
-	Utilizzo di dispositivi virtuali nell'ambiente Azure.

Negli scenari precedenti, è necessario creare una tabella di route e aggiungervi route definite dall'utente. È possibile avere più tabelle di routing e la stessa tabella di route può essere associata a una o più subnet. E ogni subnet può essere associato a una tabella singola route. Tutte le macchine virtuali e servizi cloud in uso una subnet la tabella di route associato a tale subnet.

Subnet si basano su route predefinite fino a quando non è associata alla subnet di una tabella di route. Una volta effettuata l'associazione, il routing viene eseguito in base all'algoritmo [Longest Prefix Match (LPM)](https://en.wikipedia.org/wiki/Longest_prefix_match) che seleziona il valore con il prefisso più lungo tra le route definite dall'utente e quelle predefinite. Se è presente più di una route con la stessa corrispondenza LPM una route viene selezionata in base alla sua origine nell'ordine seguente:

1.	Route definita utente
2.	Route BGP (quando viene utilizzato ExpressRoute)
3.	Route predefinita

>[AZURE.NOTE]Route definite dall'utente vengono applicate solo a servizi cloud e macchine virtuali di Azure. Ad esempio, se si vuole aggiungere un dispositivo virtuale firewall tra la rete locale e Azure, è necessario creare una route definita dall'utente per le tabelle di route di Azure per inoltrare tutto il traffico indirizzato allo spazio degli indirizzi locale al dispositivo virtuale. Tuttavia, il traffico in ingresso dallo spazio degli indirizzi locale flusso attraverso il gateway VPN o il circuito ExpressRoute direttamente all'ambiente di Azure, ignorando il dispositivo virtuale.

### Inoltro IP

Come descritto sopra, uno dei motivi principali per creare una route definita dall’utente consiste nell’inoltrare il traffico a un dispositivo virtuale. Un dispositivo virtuale non è altro che una macchina virtuale che esegue un'applicazione utilizzata per gestire il traffico di rete in qualche modo, ad esempio un firewall o un dispositivo NAT.

Questo dispositivo virtuale macchina virtuale deve essere in grado di ricevere traffico in ingresso non viene indirizzato a se stesso. Per consentire una macchina virtuale ricevere il traffico indirizzato ad altre destinazioni, è necessario abilitare l'inoltro IP nella macchina virtuale.

## Passaggi successivi
- Per informazioni su come impostare le entità di sicurezza con l'accesso corretto per usare le risorse dell'organizzazione, vedere [Autenticazione di un'entità servizio con Gestione risorse di Azure](resource-group-authenticate-service-principal.md)
- Se è necessario bloccare l'accesso a una risorsa, è possibile utilizzare i blocchi di gestione. Vedere [Bloccare le risorse con Gestione risorse di Azure](resource-group-lock-resources.md)
- Per informazioni su come configurare il routing e l'inoltro IP, vedere [Come creare route e abilitare l'inoltro IP in Azure](virtual-network/virtual-networks-udr-how-to.md). 
- Per una panoramica del controllo degli accessi in base al ruolo, vedere [Controllo degli accessi in base al ruolo nel portale di Microsoft Azure](role-based-access-control-configure.md).

<!---HONumber=July15_HO5-->