<properties title="Soluzioni di sicurezza per Macchine virtuali di Azure" pageTitle="Soluzioni di sicurezza per Macchine virtuali di Azure" description="Panoramica rapida delle funzionalit&agrave; di sicurezza principali per le macchine virtuali di Azure e collegamenti a informazioni dettagliate" metaKeywords="" services="virtual machines" solutions="" documentationCenter="" authors="kathydav" manager="timlt" videoId="" scriptId="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-multiple" ms.devlang="na" ms.topic="article" ms.date="7/29/2014" ms.author="kathydav" />

# Soluzioni di sicurezza per Macchine virtuali di Azure

Le attività e la configurazione per la sicurezza per le macchine virtuali di Azure sono in larga misura le stesse che si applicano a qualsiasi macchina virtuale o computer fisico. Includono strategie basilari come l'uso di password complesse, l'uso di nomi non noti e la definizione di un criterio e di una pianificazione per l'aggiornamento del sistema operativo e delle applicazioni. Di seguito sono anche indicate le procedure consigliate e le funzionalità da usare per implementarle:

-   Eseguire un software antivirus/antimalware

-   Usare elenchi di controllo di accesso di rete sugli endpoint

## Eseguire un software antivirus/antimalware

Azure offre diverse opzioni per le soluzioni antivirus/antimalware, ma tali soluzioni dovranno essere gestite dall'utente, che deve ad esempio stabilire quando eseguire le analisi e installare gli aggiornamenti. È possibile aggiungere il supporto antivirus quando si crea la macchina virtuale o in una fase successiva. Attualmente sono disponibili tre soluzioni come estensioni di sicurezza, che possono essere installate in macchine virtuali sia nuove che esistenti:

-   [Come installare e configurare Symantec Endpoint Protection in una macchina virtuale di Azure][Come installare e configurare Symantec Endpoint Protection in una macchina virtuale di Azure]
-   [Come installare e configurare Trend Micro Deep Security come servizio in una macchina virtuale di Azure][Come installare e configurare Trend Micro Deep Security come servizio in una macchina virtuale di Azure]
-   [Distribuzione di soluzioni antimalware in macchine virtuali di Azure][Distribuzione di soluzioni antimalware in macchine virtuali di Azure]

## Usare elenchi di controllo di accesso di rete sugli endpoint della macchina virtuale

Gli elenchi di controllo di accesso di rete consentono di autorizzare o negare selettivamente il traffico in entrata su un endpoint della macchina virtuale. Questa funzionalità di filtro per i pacchetti garantisce un ulteriore livello di sicurezza. Per informazioni dettagliate sul funzionamento e collegamenti a istruzioni, vedere [Informazioni sugli elenchi di controllo di accesso di rete (ACL)][Informazioni sugli elenchi di controllo di accesso di rete (ACL)].

## Risorse aggiuntive

[Risorse][Risorse] in Centro protezione di Microsoft Azure

  [Come installare e configurare Symantec Endpoint Protection in una macchina virtuale di Azure]: http://go.microsoft.com/fwlink/p/?LinkId=404207
  [Come installare e configurare Trend Micro Deep Security come servizio in una macchina virtuale di Azure]: http://go.microsoft.com/fwlink/p/?LinkId=404206
  [Distribuzione di soluzioni antimalware in macchine virtuali di Azure]: http://azure.microsoft.com/blog/2014/05/13/deploying-antimalware-solutions-on-azure-virtual-machines/
  [Informazioni sugli elenchi di controllo di accesso di rete (ACL)]: http://go.microsoft.com/fwlink/?LinkId=506655
  [Risorse]: http://azure.microsoft.com/it-it/support/trust-center/resources/
