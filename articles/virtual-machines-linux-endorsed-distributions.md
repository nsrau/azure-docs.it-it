<properties pageTitle="Distribuzioni approvate di Linux in Azure" description="Informazioni sulle distribuzioni di Linux approvate in Azure, che includono le linee guida per Ubuntu, OpenLogic e SUSE." services="virtual-machines" documentationCenter="" authors="szarkos" manager="timlt" editor="tysonn"/>

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="12/05/2014" ms.author="szark"/>





#Distribuzioni di Linux supportate da Azure

Le immagini di distribuzione disponibili nella raccolta di Azure vengono fornite dai partner seguenti e Microsoft collabora con le diverse community Linux per offrire un numero sempre maggiore di distribuzioni supportate. Nel frattempo, per le distribuzioni non disponibili nella raccolta, è possibile usare la propria distribuzione di Linux, attenendosi alle indicazioni disponibili in [questa pagina](../virtual-machines-linux-create-upload-vhd/).

## Canonical ##
 
[http://www.ubuntu.com/cloud/azure](http://www.ubuntu.com/cloud/azure)

Le competenze di Canonical in ambito di engineering e le regole di governance della community aperta sono alla base del successo di Ubuntu nelle tecnologie per client, server e cloud computing, inclusi servizi cloud personali per i consumatori. La visione di Canonical per una piattaforma gratuita unificata in Ubuntu, dal telefono al cloud, con una serie di interfacce coerenti per telefono, tablet, TV e computer desktop, rende Ubuntu la scelta ottimale per enti di diverso tipo, dai provider di cloud pubblico ai produttori di dispositivi elettronici per consumatori, oltre ai singoli esperti di IT.

Grazie a centri per lo sviluppo e l'engineering in tutto il mondo, Canonical si trova in una posizione privilegiata ed è pertanto in grado di collaborare con produttori di hardware e sviluppatori di software, in modo da portare le soluzioni Ubuntu sul mercato, da PC a server e dispositivi palmari.

## OpenLogic ##
 
[http://www.openlogic.com/azure](http://www.openlogic.com/azure)

OpenLogic è uno dei principali fornitori di soluzioni aziendali open source per il cloud e per data center. OpenLogic aiuta centinaia di aziende leader di vari settori di mercato ad acquisire, supportare e controllare software open source. OpenLogic offre supporto tecnico a livello commerciale e risarcimenti per 600 pacchetti open supportati dalla community di esperti di OpenLogic, incluso supporto a livello aziendale per CentOS. È inoltre il partner di avvio per la fornitura di immagini basate su CentOS in Azure.

## Oracle##
[http://www.oracle.com/technetwork/topics/cloud/faq-1963009.html](http://www.oracle.com/technetwork/topics/cloud/faq-1963009.html)

La strategia di Oracle consiste nell'offrire una vasta gamma di soluzioni per cloud pubblici e privati, offrendo al tempo stesso ai clienti la possibilità di scelta e la flessibilità necessarie per determinare la modalità di distribuzione di software Oracle nel cloud Oracle e in cloud di altro tipo.  La collaborazione di Oracle con Microsoft permette ai clienti di distribuire il software Oracle nei cloud Microsoft pubblici e privati, con la sicurezza offerta dalla certificazione e dal supporto Oracle.  L'impegno e gli investimenti di Oracle in ambito di soluzioni per cloud Oracle pubblico e privato rimangono invariati.

##SUSE##
 
[http://www.suse.com/suse-linux-enterprise-server-on-azure](http://www.suse.com/suse-linux-enterprise-server-on-azure)

SUSE Linux Enterprise Server su Azure è una piattaforma collaudata che offre un'affidabilità e una sicurezza superiori per il cloud computing. La piattaforma Linux versatile di SUSE consente l'integrazione lineare con i servizi cloud di Azure per offrire un ambiente cloud facilmente gestibile. Grazie alle oltre 9.200 applicazioni certificate create da oltre 1.800 fornitori indipendenti di software per SUSE Linux Enterprise Server, SUSE è in grado di assicurare che i carichi di lavoro in esecuzione supportati nei data center possano essere distribuiti con sicurezza su Azure.

## Versioni supportate ##

Nella tabella seguente vengono mostrate le diverse versioni delle distribuzioni, i driver di Linux Integration Services (LIS) e le versioni di Azure Linux Agent di cui è stato eseguito il test per il funzionamento su Azure. I driver LIS sono incorporati per impostazione predefinita nel kernel della distribuzione oppure sono disponibili in [questa pagina](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409). Le versioni di Linux Agent sono disponibili dall'archivio dei pacchetti della distribuzione oppure in [Github](https://github.com/azure/walinuxagent).

Questa tabella include anche un collegamento alla [patch di compatibilità per il kernel Linux](http://go.microsoft.com/fwlink/?LinkID=403027&clcid=0x409) necessaria per assicurare il funzionamento ottimale di alcune versioni di distribuzione/kernel in Azure.

<table border="1" width="600">
  <tr bgcolor="#E9E7E7">
		<th>Distribuzione</th>		
	    <th>Versione</th>
	    <th>Driver</th>
		<th>Patch per la compatibilità del kernel</th>
		<th>Agente</th>
			</tr>
	<tr>
		<th>  Canonical Ubuntu </th>
		<td> Ubuntu 12.04.1+, 14.04 e 14.10 </td>
		<td>Nel kernel</td>
		<td><a href="http://go.microsoft.com/fwlink/?LinkID=275152&amp;clcid=0x409">Obbligatoria solo per 12.04 o 12.04.01</a></td>
		<td>Pacchetto: Nell'archivio del pacchetto sotto walinuxagent <br />
			Source: <a href="http://go.microsoft.com/fwlink/p/?LinkID=250998">GitHub</a></td>
			</tr>
	<tr>
		<th> CentOS di OpenLogic </th>
		<td> CentOS 6.3+</td>
	    <td>CentOS 6.3: <a href="http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409">Driver LIS</a>; Driver CentOS 6.4+: in Kernel</td>
		<td><a href="http://go.microsoft.com/fwlink/?LinkID=275153&amp;clcid=0x409">Obbligatoria solo per 6.3</a></td>
		<td>Pacchetto: nell'<a href="http://olcentgbl.trafficmanager.net/openlogic/6/openlogic/x86_64/RPMS/">archivio del pacchetto Open Logic </a> sotto walinuxagent<br />
			Source: <a href="http://go.microsoft.com/fwlink/p/?LinkID=250998">GitHub</a></td>
 		

	</tr>
	<tr>
		<th> <a href="https://coreos.com/docs/running-coreos/cloud-providers/azure/">CoreOS</a> </th>
		<td> 494.4.0+ </td>
        <td> Nel kernel </td>
		<td> N/D </td>
		<td> Source: <a href="https://github.com/coreos/coreos-overlay/tree/master/app-emulation/wa-linux-agent">GitHub</a></td>
		
	</tr>
	<tr>
		<th> Oracle Linux </th>
		<td> 6.4+</td>
        <td>Nel kernel</td>
		<td>N/D</td>
		<td>Pacchetto: nell'archivio, nome: WALinuxAgent<br />
			Source: <a href="http://go.microsoft.com/fwlink/p/?LinkID=250998">GitHub</a></td>
		
	</tr>
	<tr>
		<th> SUSE Linux Enterprise </th>
		<td> SLES 11 SP3+</td>
        <td>Nel kernel</td>
		<td>N/D</td>
		<td>Pacchetto: In <a href="https://build.opensuse.org/project/show/Cloud:Tools" >Cloud: strumenti</a> archivio, nome: WALinuxAgent<br />
			Codice sorgente: <a href="http://go.microsoft.com/fwlink/p/?LinkID=250998">GitHub</a></td>
		
	</tr>
	<tr>
		<th> openSUSE </th>
		<td> openSUSE 13.1+</td>
		<td>Nel kernel</td>
		<td>N/D</td>
		<td>Pacchetto: In <a href="https://build.opensuse.org/project/show/Cloud:Tools" >Cloud: strumenti</a> archivio, nome: WALinuxAgent<br />
			Codice sorgente: <a href="http://go.microsoft.com/fwlink/p/?LinkID=250998">GitHub</a></td>
		
	</tr>
</table>





<!--HONumber=42-->
