---
title: "Proteggere i dati personali con le funzionalità di sicurezza di rete di Azure | Microsoft Docs"
description: "Proteggere i dati personali usando le funzionalità di sicurezza di rete di Azure"
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
ms.translationtype: HT
ms.sourcegitcommit: 1c730c65194e169121e3ad1d1423963ee3ced8da
ms.openlocfilehash: 81aec340d595371a314ec0c1361f77a37c406a1e
ms.contentlocale: it-it
ms.lasthandoff: 08/30/2017

---
# <a name="protect-personal-data-with-network-security-features-azure-application-gateway-and-network-security-groups"></a>Proteggere i dati personali con le funzionalità di sicurezza di rete: gruppi di sicurezza di rete e gateway applicazione di Azure

Questo articolo contiene informazioni e procedure utili per usare i gruppi di sicurezza di rete e il gateway applicazione di Azure per proteggere i dati personali.

Un elemento importante in una strategia di sicurezza a più livelli per la protezione della privacy dei dati personali è la difesa contro gli exploit associati a vulnerabilità comuni, come gli attacchi SQL injection o cross-site scripting. Mantenere il traffico di rete indesiderato all'esterno della rete virtuale di Azure aiuta a proteggersi dalla potenziale compromissione dei dati sensibili e Microsoft Azure offre strumenti utili per proteggere i dati dagli utenti malintenzionati.

## <a name="scenario"></a>Scenario

Un'importante compagnia di viaggi in crociera, con sede negli Stati Uniti, sta espandendo le proprie operazioni per offrire itinerari nel Mar Mediterraneo e nel Mar Baltico, nonché nelle isole britanniche. Per supportare tale iniziativa, ha acquistato diverse linee minori con sede in Italia, Germania, Danimarca e Regno Unito.

La società usa Microsoft Azure per archiviare i dati aziendali nel cloud ed eseguire le applicazioni in macchine virtuali che accedono a tali dati e li elaborano. I dati includono informazioni personali come nomi, indirizzi, numeri di telefono e dati delle carte di credito della base clienti globale, nonché informazioni tradizionali del reparto risorse umane come indirizzi, numeri di telefono, codici fiscali e altri dati dei dipendenti aziendali in tutte le sedi. La linea di crociere gestisce anche un database di grandi dimensioni dei membri dei programmi fedeltà e premi, che include informazioni personali per tenere traccia delle relazioni con i clienti attuali e del passato.

I dipendenti aziendali accedono alla rete dagli uffici remoti della società, mentre agenti di viaggio in tutto il mondo hanno accesso ad alcune risorse aziendali e usano applicazioni basate sul Web ospitate in VM di Azure per interagirvi.

## <a name="problem-statement"></a>Presentazione del problema

L'azienda deve proteggere la privacy dei dati personali dei clienti e dei dipendenti dagli utenti malintenzionati che sfruttano le vulnerabilità del software per eseguire codice dannoso che potrebbe esporre i dati personali archiviati o usati dalle applicazioni basate sul cloud dell'azienda.

## <a name="company-goal"></a>Obiettivo dell'azienda

L'obiettivo dell'azienda è assicurarsi che utenti non autorizzati non possano accedere alle reti virtuali di Azure aziendali e alle applicazioni e ai dati che vi risiedono sfruttando vulnerabilità comuni. 

## <a name="solutions"></a>Soluzioni

Microsoft Azure offre meccanismi di sicurezza che consentono di impedire al traffico indesiderato di accedere alle reti virtuali di Azure. Il controllo del traffico in ingresso e in uscita è in genere eseguito da firewall. In Azure è possibile usare il gateway applicazione con il Web application firewall e i gruppi di sicurezza di rete (NSG), che fungono da semplice firewall distribuito. Questi strumenti consentono di rilevare e bloccare il traffico di rete indesiderato.

### <a name="application-gatewayweb-application-firewall"></a>Gateway applicazione/Web application firewall

Il componente [Web application firewall](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) (WAF) del [gateway applicazione di Azure](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) protegge le applicazioni Web, che sono sempre più spesso oggetto di attacchi che sfruttano vulnerabilità comuni note. Un Web application firewall centralizzato permette di proteggersi dagli attacchi Web e semplifica la gestione della sicurezza senza richiedere alcuna modifica alle applicazioni.

Il Web application firewall di Azure gestisce varie categorie di attacco, tra cui SQL injection, cross-site scripting, violazioni e anomalie del protocollo HTTP, bot, crawler, scanner, errori di configurazione comuni delle applicazioni, Denial of Service HTTP e altri attacchi comuni come inserimento di comandi, HTTP Request Smuggling, HTTP Response Splitting e Remote File Inclusion. 

È possibile creare un gateway applicazione con WAF oppure aggiungere il Web application firewall a un gateway applicazione esistente. In entrambi i casi, il gateway applicazione di Azure richiede una propria subnet.

#### <a name="how-do-i-create-an-application-gateway-with-waf"></a>Come creare un gateway applicazione con WAF 

Per creare un nuovo gateway applicazione con WAF abilitato, seguire questa procedura:

1. Accedere al portale di Azure e nel riquadro **Preferiti** del portale fare clic su **Nuovo**.

2. Nel pannello **Nuovo** fare clic su **Rete**.

3. Fare clic su **Gateway applicazione**.

4. Passare al portale di Azure e fare clic su **Nuovo \> Rete \> Gateway applicazione**.

   ![Creazione di gateway applicazione](media/protect-netsec/app-gateway-01.png)

5. Nel pannello **Informazioni di base** visualizzato immettere i valori per i campi seguenti: Nome, Livello (Standard o WAF), Dimensioni SKU (Small, Medium o Large), Numero di istanze (2 per la disponibilità elevata), Sottoscrizione, Gruppo di risorse e Località.

6. Nel pannello **Impostazioni** che viene visualizzato in **Rete virtuale**, fare clic su **Scegliere una rete virtuale**. Con questo passaggio si apre il pannello Scegli rete virtuale.

7. Fare clic su **Crea nuovo** per aprire il pannello **Crea rete virtuale**.

8. Immettere i valori seguenti: Nome, Spazio indirizzi, Nome subnet e Intervallo di indirizzi subnet. Fare clic su **OK**.

9. Nel pannello **Impostazioni** scegliere il tipo di indirizzo IP in **Configurazione IP front-end**.

10. Fare clic su **Scegliere un indirizzo IP pubblico** e quindi su **Crea nuovo**.

11. Accettare il valore predefinito e fare clic su **OK**.

12. Nel pannello **Impostazioni** selezionare l'uso di HTTP o HTTPS in **Protocollo** sotto **Configurazione listener**. Per usare HTTPS è necessario un certificato.

13. Configurare le impostazioni specifiche del Web application firewall: **Stato firewall** (**Abilitato**) e **Modalità firewall** (**Prevenzione**). Se si sceglie la modalità **Rilevamento**, il traffico viene solo registrato.

14. Esaminare la pagina **Riepilogo** e fare clic su **OK**. Il gateway applicazione verrà inserito in coda e creato.

Al termine della creazione del gateway applicazione, passare al gateway applicazione nel portale per proseguirne la configurazione.

![Gateway applicazione creato](media/protect-netsec/adatum-app-gateway.png)

#### <a name="how-do-i-add-waf-to-an-existing-application"></a>Come aggiungere il Web application firewall a un'applicazione esistente

Per aggiornare un gateway applicazione in modo da supportare il Web application firewall in modalità prevenzione, seguire questa procedura:

1. Nel riquadro **Preferiti** del portale di Azure fare clic su **Tutte le risorse**.

2. Fare clic sul gateway applicazione esistente nel pannello **Tutte le risorse**. 
>[!NOTE]
Se nella sottoscrizione selezionata sono già presenti diverse risorse, è possibile immettere il nome nella casella Filtra per nome per accedere facilmente alla zona DNS.
3. Fare clic su **Web application firewall** e aggiornare le impostazioni del gateway applicazione: **Aggiorna al livello WAF** (selezionata), **Stato firewall** (Abilitato) e **Modalità firewall** (Prevenzione). È anche necessario configurare il set di regole e le regole disabilitate.

Per informazioni più dettagliate su come creare un nuovo gateway applicazione con WAF e come aggiungere il Web application firewall a un gateway applicazione esistente, vedere [Creare un gateway applicazione con Web application firewall usando il portale](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal).

### <a name="network-security-groups"></a>Gruppi di sicurezza di rete

Un [gruppo di sicurezza di rete](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) (NSG) contiene un elenco di regole di sicurezza che consentono o rifiutano il traffico di rete verso le risorse connesse a [reti virtuali di Azure](https://docs.microsoft.com/azure/virtual-network/). I gruppi di sicurezza di rete possono essere associati a subnet o singole VM. Quando un gruppo di sicurezza di rete è associato a una subnet, le regole si applicano a tutte le risorse connesse alla subnet. Il traffico può essere ulteriormente limitato associando un gruppo di sicurezza di rete anche a una VM o un'interfaccia di rete.

I gruppi di sicurezza di rete contengono quattro proprietà: nome, area, gruppo di risorse e regole.
>[!Note]
Anche se un gruppo di sicurezza di rete si trova in un gruppo di risorse, può essere associato a risorse di qualsiasi gruppo di risorse, a condizione che la risorsa faccia parte della stessa area di Azure del gruppo di sicurezza di rete.

Le regole dei gruppi di sicurezza di rete contengono nove proprietà: nome, protocollo (TCP, UDP o \*, che include sia ICMP che UDP e TCP), intervallo di porte di origine, intervallo di porte di destinazione, prefisso dell'indirizzo di origine, prefisso dell'indirizzo di destinazione, direzione (in ingresso o in uscita), priorità (tra 100 e 4096) e tipo di accesso (consenso o rifiuto). Tutti i gruppi di sicurezza di rete contengono un set di regole predefinite che possono essere eliminate o sostituite dalle regole create dall'utente.

#### <a name="how-do-i-implement-nsgs"></a>Come implementare gruppi di sicurezza di rete

L'implementazione di gruppi di sicurezza di rete richiede pianificazione nonché di tenere conto di diverse considerazioni relative alla progettazione, che includono i limiti al numero di gruppi di sicurezza di rete per sottoscrizione e di regole per gruppo di sicurezza di rete, la progettazione di reti virtuali e subnet, le regole speciali, il traffico ICMP, l'isolamento dei livelli con le subnet, i servizi di bilanciamento del carico e altro ancora.

Per altre indicazioni per la pianificazione e l'implementazione di gruppi di sicurezza di rete e uno scenario di distribuzione di esempio, vedere [Filtrare il traffico di rete con gruppi di sicurezza di rete](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).

#### <a name="how-do-i-create-rules-in-an-nsg"></a>Come creare regole in un gruppo di sicurezza di rete

Per creare regole in ingresso in un gruppo di sicurezza di rete esistente, seguire questa procedura:

1. Fare clic su **Esplora** e quindi su **Gruppi di sicurezza di rete**.

2. Nell'elenco dei gruppi di sicurezza di rete fare clic su **NSG-FrontEnd** e quindi su **Regole di sicurezza in ingresso**.

3. Nell'elenco delle regole di sicurezza in ingresso fare clic su **Aggiungi**.

4. Immettere i valori nei campi seguenti: Nome, Priorità, Origine, Protocollo, Intervallo di porte di origine, Destinazione, Intervallo di porte di destinazione e Azione.

La nuova regola verrà visualizzata nel gruppo di sicurezza di rete dopo pochi secondi.

![Regole di sicurezza di rete](media/protect-netsec/inbound-security.png)

Per altre istruzioni su come creare gruppi di sicurezza di rete nelle subnet, creare regole e associare un gruppo di sicurezza di rete a una subnet front-end e back-end, vedere [Creare gruppi di sicurezza di rete mediante il portale di Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-create-nsg-arm-pportal).

## <a name="next-steps"></a>Passaggi successivi

[Azure Network Security (Sicurezza di rete di Azure)](https://azure.microsoft.com/blog/azure-network-security/)

[Procedure consigliate per la sicurezza della rete di Azure](https://docs.microsoft.com/azure/security/azure-security-network-security-best-practices)

[Ottenere informazioni su un gruppo di sicurezza di rete](https://docs.microsoft.com/rest/api/network/virtualnetwork/get-information-about-a-network-security-group)

[Web application firewall (WAF)](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview)

