---
title: Risoluzione dei problemi delle appliance virtuali di rete in Azure | Microsoft Docs
description: Informazioni su come risolvere i problemi delle appliance virtuali di rete in Azure.
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: ''
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/26/2018
ms.author: genli
ms.openlocfilehash: 13cec39278577a818ef43f1215fd2e6653f15ed2
ms.sourcegitcommit: cd0a1514bb5300d69c626ef9984049e9d62c7237
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/30/2018
ms.locfileid: "52678441"
---
#  <a name="network-virtual-appliance-issues-in-azure"></a>Problemi delle appliance virtuali di rete in Azure

È possibile che si verifichino problemi ed errori di connettività per la VM o la VPN quando si usa un'appliance virtuale di rete in Microsoft Azure. Questo articolo illustra le procedure di base che consentono di convalidare i requisiti essenziali della piattaforma Azure per le configurazioni delle appliance virtuali di rete.

Il supporto tecnico per le appliance virtuali di rete di terze parti e per la rispettiva integrazione con la piattaforma Azure viene fornito dal fornitore dell'appliance virtuale di rete. 

> [!NOTE]
> Se si verifica un problema di connettività o di routing che interessa un'appliance virtuale di rete, è necessario [contattare il fornitore dell'appliance virtuale di rete](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines) direttamente.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="checklist-for-troubleshooting-with-nva-vendor"></a>Elenco di controllo per la risoluzione dei problemi con il fornitore dell'appliance virtuale di rete

- Aggiornamenti software per il software di VM per appliance virtuali di rete
- Configurazione e funzionalità dell'account del servizio
- Route definite dall'utente nelle subnet di rete virtuale che indirizzano il traffico all'appliance virtuale di rete
- Route definite dall'utente nelle subnet di rete virtuale che indirizzano il traffico dall'appliance virtuale di rete
- Tabelle e regole di routing entro l'appliance virtuale di rete (ad esempio, da NIC1 a NIC2)
- Traccia sulle schede di interfaccia di rete dell'appliance virtuale di rete per la verifica della ricezione e dell'invio del traffico di rete
- Quando si usano uno SKU Standard e IP pubblici, è necessario che esistano un gruppo di sicurezza di rete e una regola esplicita per consentire l'indirizzamento del traffico all'appliance virtuale di rete.

## <a name="basic-troubleshooting-steps"></a>Procedura di base per la risoluzione dei problemi

- Controllo della configurazione di base
- Controllo delle prestazioni dell'appliance virtuale di rete
- Risoluzione avanzata dei problemi di rete

## <a name="check-the-minimum-configuration-requirements-for-nvas-on-azure"></a>Controllare i requisiti di configurazione minimi per le appliance virtuali di rete in Azure

Per il funzionamento corretto in Azure, a ogni appliance virtuale di rete sono associati requisiti di configurazione di base. La sezione seguente fornisce la procedura da seguire per verificare tali configurazioni di base. Per altre informazioni, [contattare il fornitore dell'appliance virtuale di rete](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines).

**Controllare se l'inoltro IP è abilitato nell'appliance virtuale di rete**

Usare il portale di Azure

1.  Individuare la risorsa dell'appliance virtuale di rete nel [portale di Azure](https://portal.azure.com), selezionare Rete e quindi selezionare l'interfaccia di rete.
2.  Nella pagina Interfaccia di rete selezionare Configurazione IP.
3.  Assicurarsi che l'inoltro IP sia abilitato.

Usare PowerShell

1. Aprire PowerShell e quindi accedere all'account Azure.
2. Eseguire il comando seguente, sostituendo i valori tra parentesi con le informazioni specifiche per lo scenario dell'utente:

        Get-AzureRmNetworkInterface -ResourceGroupName <ResourceGroupName> -Name <NicName>  

3. Controllare la proprietà **EnableIPForwarding**.
 
4. Se l'inoltro IP non è abilitato, eseguire i comandi seguenti per abilitarlo:

          $nic2 = Get-AzureRmNetworkInterface -ResourceGroupName <ResourceGroupName> -Name <NicName>
          $nic2.EnableIPForwarding = 1
          Set-AzureRmNetworkInterface -NetworkInterface $nic2
          Execute: $nic2 #and check for an expected output:
          EnableIPForwarding   : True
          NetworkSecurityGroup : null

**Verificare la presenza di un gruppo di sicurezza di rete quando si usano IP pubblici e SKU Standard** Quando si usano uno SKU Standard e IP pubblici, è necessario che esistano un gruppo di sicurezza di rete e una regola esplicita per consentire il traffico verso l'appliance virtuale di rete.

**Controllare se il traffico può essere indirizzato all'appliance virtuale di rete**

1. Nel [portale di Azure](https://portal.azure.com) aprire **Network Watcher** e quindi selezionare **Hop successivo**.
2. Specificare una macchina virtuale configurata per reindirizzare il traffico all'appliance virtuale di rete e un indirizzo IP di destinazione in corrispondenza del quale visualizzare l'hop successivo. 
3. Se l'appliance virtuale di rete non è incluso nell'elenco come **hop successivo**, controllare e aggiornare le tabelle di route di Azure.

**Controllare se il traffico riesce a raggiungere l'appliance virtuale di rete**

1.  Nel [portale di Azure](https://portal.azure.com) aprire **Network Watcher** e quindi selezionare **Verifica flusso IP**. 
2.  Specificare la macchina virtuale e l'indirizzo IP dell'appliance virtuale di rete e quindi controllare se il traffico viene bloccato da gruppi di sicurezza di rete.
3.  Se è presente una regola del gruppo di sicurezza di rete che blocca il traffico, individuare il gruppo di sicurezza di rete in **Regole di sicurezza effettive** e quindi aggiornarlo in modo da consentire il passaggio del traffico. Eseguire quindi di nuovo **Verifica flusso IP** e usare **Controllo della connettività** per testare le comunicazioni TCP dalla macchina virtuale all'indirizzo IP interno o esterno.

**Controllare se l'appliance virtuale di rete e le macchine virtuali sono in ascolto del traffico previsto**

1.  Connettersi all'appliance virtuale di rete usando RDP o SSH e quindi eseguire questo comando:

    Per Windows:

        netstat -an

    Per Linux:

        netstat -an | grep -i listen
2.  Se non viene visualizzata la porta TCP usata dal software dell'appliance virtuale di rete elencata nei risultati, è necessario configurare l'applicazione sull'appliance virtuale di rete e sulla macchina virtuale in modo che sia in ascolto e risponda al traffico che raggiunge tali porte. [Contattare il fornitore dell'appliance virtuale di rete per ottenere assistenza in base alla necessità](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines).

## <a name="check-nva-performance"></a>Controllare le prestazioni dell'appliance virtuale di rete

### <a name="validate-vm-cpu"></a>Convalidare la CPU della VM

Se l'utilizzo della CPU si avvicina al 100%, è possibile che si verifichino problemi che interessano i rilasci dei pacchetti di rete. La macchina virtuale segnala un utilizzo medio della CPU per un intervallo di tempo specifico nel portale di Azure. Durante un picco di utilizzo della CPU, esaminare il processo nella VM guest che provoca l'utilizzo elevato della CPU e attenuare il problema, se possibile. Potrebbe essere anche necessario ridimensionare la VM specificando dimensioni di SKU più elevate oppure, per un set di scalabilità di macchine virtuali, aumentare il numero di istanze o impostare il ridimensionamento automatico per l'utilizzo della CPU. Se si verifica uno di questi problemi, [contattare il fornitore dell'appliance virtuale di rete per ottenere assistenza](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines), in base alla necessità.

### <a name="validate-vm-network-statistics"></a>Convalidare le statistiche di rete della VM 

Se l'utilizzo della rete della VM presenta picchi o periodi di utilizzo elevato, potrebbe essere anche necessario aumentare le dimensioni di SKU della VM per ottenere funzionalità più elevate per la velocità effettiva. È anche possibile ridistribuire la macchina virtuale abilitando Rete accelerata. Per verificare che l'appliance virtuale di rete supporti la funzionalità Rete accelerata, [contattare il fornitore dell'appliance virtuale di rete per ottenere assistenza](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines), in base alla necessità.

## <a name="advanced-network-administrator-troubleshooting"></a>Risoluzione avanzata dei problemi dell'amministratore di rete

### <a name="capture-network-trace"></a>Acquisire la traccia di rete
Acquisire una traccia di rete simultanea nella macchina virtuale di origine, nell'appliance virtuale di rete e nella VM di destinazione durante l'esecuzione di **[PsPing](https://docs.microsoft.com/sysinternals/downloads/psping)** o **Nmap** e quindi arrestare la traccia.

1. Per acquisire una traccia di rete simultanea, eseguire questo comando:

    Per Windows:

        netsh trace start capture=yes tracefile=c:\server_IP.etl scenario=netconnection

    Per Linux:

        sudo tcpdump -s0 -i eth0 -X -w vmtrace.cap

2. Usare **PsPing** o **Nmap** dalla macchina virtuale di origine alla VM di destinazione, ad esempio `PsPing 10.0.0.4:80` o `Nmap -p 80 10.0.0.4`.

3. Aprire la traccia di rete dalla macchina virtuale di destinazione usando [Network Monitor](https://www.microsoft.com/download/details.aspx?id=4865) o tcpdump. Applicare un filtro di visualizzazione per l'indirizzo IP della macchina virtuale di origine da cui è stato eseguito **PsPing** o **Nmap**, ad esempio `IPv4.address==10.0.0.4 (Windows netmon)` o `tcpdump -nn -r vmtrace.cap src or dst host 10.0.0.4` (Linux).

### <a name="analyze-traces"></a>Analizzare le tracce

Se non è possibile visualizzare i pacchetti in ingresso nella traccia della macchina virtuale di back-end, è probabile che sia presente un'interferenza dovuta a un gruppo di sicurezza di rete o a una route definita dall'utente oppure che le tabelle di dell'appliance virtuale di rete non siano corrette.

Se si visualizzano i pacchetti in entrata, ma non viene restituita alcuna risposta, può essere necessario risolvere un problema di firewall o di un'applicazione di VM. Se si verifica uno di questi problemi, [contattare il fornitore dell'appliance virtuale di rete per ottenere assistenza](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines), in base alla necessità.

