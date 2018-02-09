---
title: "Test della velocità effettiva di rete della macchina virtuale di Azure | Microsoft Docs"
description: "Informazioni su come testare la velocità effettiva di rete della macchina virtuale di Azure."
services: virtual-network
documentationcenter: na
author: steveesp
manager: Gerald DeGrace
editor: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/21/2017
ms.author: steveesp
ms.openlocfilehash: d65b86cc63a4fd39824a6421afd5ce9abb7fd270
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2018
---
# <a name="bandwidththroughput-testing-ntttcp"></a>Test della larghezza di banda/velocità effettiva (NTTTCP)

Durante il test della velocità di rete effettiva in Azure, è preferibile usare uno strumento in grado di testare la rete desiderata e di ridurre al minimo l'uso di altre risorse che potrebbero influire sulle prestazioni. È consigliabile usare NTTTCP.

Copiare lo strumento in due macchine virtuali di Azure delle stesse dimensioni. Una macchina virtuale funge da MITTENTE, l'altra da RICEVITORE.

#### <a name="deploying-vms-for-testing"></a>Distribuzione di macchine virtuali per i test
Ai fini di questo test, le due macchine virtuali devono trovarsi nello stesso servizio cloud o set di disponibilità in modo da poter usare i relativi IP interni ed escludere i servizi di bilanciamento del carico dal test. È possibile eseguire il test con un indirizzo VIP, ma questa tipologia di test non rientra nell'ambito di questo documento.
 
Prendere nota dell'indirizzo IP del RICEVITORE. In questo esempio verrà definito "a.b.c.r."

Prendere nota del numero di core nella macchina virtuale. In questo esempio sarà "\#num\_cores"
 
Eseguire il test NTTTCP per 300 secondi (o 5 minuti) nella macchina virtuale mittente e in quella ricevitore.

Suggerimento: quando si configura il test per la prima volta, è possibile indicare un periodo più breve in modo da avere un riscontro in meno tempo. Una volta che lo strumento funziona come previsto, estendere il periodo di prova a 300 secondi per ottenere i risultati più accurati.

> [!NOTE]
> Il mittente **e** il destinatario devono specificare **lo stesso** parametro di durata del test (-t).

Per testare un singolo flusso TCP per 10 secondi:

Parametri ricevitore: ntttcp -r -t 10 -P 1

Parametri mittente: ntttcp -s10.27.33.7 -t 10 -n 1 -P 1

> [!NOTE]
> L'esempio precedente deve essere usato solo per confermare la configurazione. Alcuni esempi di test effettivi vengono trattati più avanti in questo documento.

## <a name="testing-vms-running-windows"></a>Test di macchine virtuali che eseguono WINDOWS:

#### <a name="get-ntttcp-onto-the-vms"></a>Installare NTTTCP nelle macchine virtuali.

Scaricare la versione più recente: <https://gallery.technet.microsoft.com/NTttcp-Version-528-Now-f8b12769>

O cercarla se spostata: <https://www.bing.com/search?q=ntttcp+download>\<. Generalmente sarà il primo risultato ottenuto

È consigliabile inserire NTTTCP in una cartella separata, ad esempio, ad esempio c:\\strumenti

#### <a name="allow-ntttcp-through-the-windows-firewall"></a>Consentire NTTTCP in Windows Firewall
Nella macchina RICEVITORE, creare una regola di assenso in Windows Firewall per consentire l'ingresso del traffico NTTTCP. È più semplice consentire l'intero programma NTTTCP usando il nome anziché consentire porte TCP in entrata specifiche.

Consentire NTTTCP in Windows Firewall nel modo seguente:

netsh advfirewall firewall add rule program=\<PERCORSO\>\\ntttcp.exe name="ntttcp" protocol=any dir=in action=allow enable=yes profile=ANY

Ad esempio, se è stato copiato ntttcp.exe nella cartella "c:\\strumenti" cartella, il comando sarà il seguente: 

netsh advfirewall firewall add rule program=c:\\strumenti\\ntttcp.exe name="ntttcp" protocol=any dir=in action=allow enable=yes profile=ANY

#### <a name="running-ntttcp-tests"></a>Esecuzione di test NTTTCP

Avviare NTTTCP sul RICEVITORE (**da CMD**, non da PowerShell):

ntttcp -r –m [2\*\#num\_cores],\*,a.b.c.r -t 300

Se la macchina virtuale dispone di quattro core e il suo indirizzo IP è 10.0.0.4, il comando sarà simile al seguente:

ntttcp -r –m 8,\*,10.0.0.4 -t 300


Avviare NTTTCP nel MITTENTE (**da CMD**, non da PowerShell):

ntttcp -s –m 8,\*,10.0.0.4 -t 300 

Attendere i risultati.


## <a name="testing-vms-running-linux"></a>Test di macchine virtuali che eseguono LINUX:

Usare nttcp-for-linux, disponibile all'indirizzo <https://github.com/Microsoft/ntttcp-for-linux>

Nelle macchine virtuali Linux (MITTENTE e RICEVITORE), eseguire i comandi seguenti per preparare ntttcp-for-linux:

CentOS - Installare Git:
``` bash
  yum install gcc -y  
  yum install git -y
```
Ubuntu - Installare Git:
``` bash
 apt-get -y install build-essential  
 apt-get -y install git
```
Verificare e installare in entrambie le macchine virtuali:
``` bash
 git clone https://github.com/Microsoft/ntttcp-for-linux
 cd ntttcp-for-linux/src
 make && make install
```

Come illustrato nell'esempio di Windows, si presuppone che l'indirizzo IP del RICEVITORE Linux sia 10.0.0.4

Avviare NTTTCP-for-Linux nel RICEVITORE:

``` bash
ntttcp -r -t 300
```

Nel MITTENTE, eseguire:

``` bash
ntttcp -s10.0.0.4 -t 300
```
 
Il test ha una lunghezza predefinita di 60 secondi se non viene specificato un parametro di tempo

## <a name="testing-between-vms-running-windows-and-linux"></a>Test tra macchine virtuali che eseguono Windows e LINUX:

In questi scenari si dovrebbe attivare la modalità no-sync per consentire l'esecuzione del test. Questa operazione viene eseguita usando il **flag -N** per Linux e il **flag -ns** per Windows.

#### <a name="from-linux-to-windows"></a>Da Linux a Windows:

Ricevitore <Windows>:

``` bash
ntttcp -r -m <2 x nr cores>,*,<Windows server IP>
```

Mittente <Linux>:

``` bash
ntttcp -s -m <2 x nr cores>,*,<Windows server IP> -N -t 300
```

#### <a name="from-windows-to-linux"></a>Da Windows a Linux:

Ricevitore <Linux>:

``` bash
ntttcp -r -m <2 x nr cores>,*,<Linux server IP>
```

Mittente <Windows>:

``` bash
ntttcp -s -m <2 x nr cores>,*,<Linux  server IP> -ns -t 300
```
## <a name="testing-cloud-service-instances"></a>Test di istanze del servizio cloud:
È necessario aggiungere la sezione seguente in ServiceDefinition.csdef
```xml
<Endpoints>
  <InternalEndpoint name="Endpoint3" protocol="any" />
</Endpoints> 
```

## <a name="next-steps"></a>Passaggi successivi
* In base ai risultati, potrebbe esserci la possibilità di [ottimizzare la velocità di rete effettiva dei computer](virtual-network-optimize-network-bandwidth.md) per lo scenario specifico.
* Sono disponibili informazioni su come [allocare la larghezza di banda alle macchine virtuali] (virtual-machine-network-throughput.md).
* Altre informazioni sono disponibili nell'articolo [Domande frequenti sulla rete virtuale di Azure](virtual-networks-faq.md).
