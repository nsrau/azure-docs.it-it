<properties 
	pageTitle="Informativa sulla privacy per Azure Site Recovery" 
	description="Descrive un'ulteriore informativa sulla privacy per Azure Site Recovery" 
	services="site-recovery" 
	documentationCenter="" 
	authors="raynew" 
	manager="jwhit" 
	editor="tysonn"/>

<tags 
	ms.service="site-recovery" 
	ms.workload="backup-recovery" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/19/2015" 
	ms.author="raynew"/>

# Informativa sulla privacy per Azure Site Recovery

Questa tabella fornisce altre informazioni sulla privacy per il servizio Site Recovery di Microsoft Azure ("Servizio"). Per visualizzare l'informativa sulla privacy per i servizi Microsoft Azure, vedere l'
[Informativa sulla privacy di Microsoft Azure](http://go.microsoft.com/fwlink/?LinkId=324899)

<table>
<thead>
<tr>
	<th>Funzionalità</th>
	<th>Risultato</th>
	<th>Informazioni raccolte</th>
	<th>Uso</th>
	<th>Scelta</th>
</tr>
</thead>
<tbody>
<tr>
	<td><p><b>Registrazione</b></p></td>
	<td><p>Registrare il server nel servizio in modo da proteggere le macchine virtuali</p></td>
	<td><p>Dopo la registrazione, un servizio raccoglie, elabora e trasmette le informazioni seguenti:</p>
		<ul>
			<li>Informazioni sul certificato di gestione dal server VMM designato per fornire funzionalità di ripristino di emergenza usando il nome del servizio del server VMM</li>
			<li>Nome dei cloud di macchine virtuali nel server VMM in uso</li>
		</ul>
</td>
	<td><p>Il Servizio usa le informazioni citate sopra nel modo seguente:</p>
		<ul>
			<li>Certificato di gestione: viene usato per identificare e autenticare il server VMM registrato per accedere al Servizio. Il servizio usa la parte della chiave pubblica del certificato per fornire un token a cui solo il server VMM registrato può accedere. Il server dovrà usare questo token per accedere alle funzionalità del Servizio.</li>
			<li>Nome del server VMM: il nome del server VMM è necessario per identificare e comunicare con il server VMM appropriato in cui si trovano i cloud. 
			</li>
			<li>Nomi dei cloud dal server VMM: il nome del cloud è obbligatorio quando si usa la funzionalità di associazione/annullamento associazione descritta di seguito. Quando si decide di associare il cloud da un data center principale con un altro cloud nel data center di ripristino, vengono presentati i nomi di tutti i cloud dal data center di ripristino.</li>
		</ul>
</td>
	<td><p>Queste informazioni sono una parte essenziale del processo di registrazione del Servizio perché consentono all'utente e al Servizio di identificare il server VMM per il quale si desidera garantire la protezione di Azure Site Recovery e di identificare il server VMM registrato corretto. Se non si vogliono inviare queste informazioni al Servizio, non usare questo Servizio. Se si registra il server e successivamente si desidera annullare la registrazione, è possibile farlo eliminando le informazioni sul server VMM dal portale del Servizio, ovvero il portale di Azure.</p></td>
</tr>

<tr>
	<td><p><b>Abilitare la protezione di Azure Site Recovery</b></p></td>
	<td><p>Il provider di Azure Site Recovery installato nel server VMM è il canale per le comunicazioni con il Servizio. Il provider è una DLL (Dynamic Link Library, libreria di collegamento dinamico) ospitata nel processo VMM. Al termine dell'installazione del provider, la funzionalità "Datacenter Recovery" viene abilitata nella Console di amministrazione VMM. In tutte le macchine virtuali nuove o esistenti in un cloud è possibile abilitare una proprietà denominata "Datacenter Recovery" per garantire la protezione della macchina virtuale. Dopo che questa proprietà è stata impostata, il provider invia il nome e l'ID della macchina virtuale al Servizio. La protezione virtuale basata sulla tecnologia di replica Hyper-V disponibile in Windows Server 2012 o in Windows Server 2012 R2. I dati delle macchine virtuali vengono replicati da un host Hyper-V a un altro, generalmente situato in un data center di "ripristino" diverso.</p></td>
	<td><p>Il Servizio raccoglie, elabora e trasmette i metadati per la macchina virtuale, che includono nome, ID, rete virtuale e nome del cloud a cui appartiene.</p>
	</td>
	<td><p>Il Servizio usa i dati citati sopra per popolare le informazioni della macchina virtuale nel portale del Servizio.</p>
	</td>
	<td><p>Poiché si tratta di una parte essenziale del servizio, questa funzionalità non può essere disabilitata. Se non si vuole che queste informazioni vengano inviate al Servizio, non abilitare la protezione di Azure Site Recovery per le macchine virtuali. Si noti che tutti i dati inviati dal provider al servizio vengono trasmessi tramite HTTPS.</p></td>
</tr>
<tr>
	<td><p><b>Piano di ripristino</b></p></td>
	<td><p>Questa funzionalità consente di creare un piano di orchestrazione per il data center di "ripristino". È possibile definire l'ordine con cui le macchine virtuali o un gruppo di macchine virtuali deve essere avviato presso il sito di ripristino. È inoltre possibile specificare l'esecuzione di script automatizzati o di azioni manuali al momento del ripristino per ogni macchina virtuale. Il failover (illustrato nella sezione successiva) viene in genere attivato a livello del piano di ripristino per un ripristino coordinato.</p></td>
	<td><p>Il Servizio raccoglie, elabora e trasmette le informazioni seguenti come parte della funzionalità Piano di ripristino:</p>
		<ul>
			<li>Piano di ripristino, inclusi i metadati della macchina virtuale</li>
			<li>Metadati dello script di automazione o la nota di azione manuale.</li>
		</ul>
	</td>
	<td><p>I metadati descritti in precedenza sono usati per creare il piano di ripristino nel portale del Servizio.</p>
	</td>
	<td><p>Poiché si tratta di una parte essenziale del servizio, questa funzionalità non può essere disabilitata. Se non si vogliono inviare queste informazioni al Servizio, non creare piani di ripristino in questo Servizio.</p></td>
</tr>
<tr>
	<td><p><b>Mapping di rete</b></p></td>
	<td><p>Questa funzionalità consente di eseguire il mapping delle informazioni di rete dal data center principale al data center di ripristino. Quando le macchine virtuali sono ripristinate presso il sito di ripristino, il mapping consente di ristabilire la connettività di rete per esse.</p></td>
	<td><p>Nell'ambito della funzionalità di mapping di rete, il Servizio raccoglie, elabora e trasmette i metadati delle reti logiche per ogni sito (primario e data center).</p>
	</td>
	<td><p>Il Servizio usa i metadati per popolare il portale del Servizio in cui è possibile eseguire il mapping delle informazioni di rete.</p>
	</td>
	<td><p>Poiché si tratta di una parte essenziale del Servizio, questa funzionalità non può essere disabilitata. Se non si desidera che queste informazioni vengano inviate al Servizio, non usare la funzionalità di mapping di rete.</p></td>
</tr>
<tr>
	<td><p><b>Failover (pianificato, non pianificato e di test)</b></p></td>
	<td><p>Questa funzionalità consente di eseguire il failover di una macchina virtuale da un data center gestito VMM a un altro data center gestito VMM. L'azione di failover viene attivata dall'utente nel portale del Servizio. Alcune possibili cause di failover possono essere un evento non pianificato (ad esempio, una calamità naturale), un evento pianificato (ad esempio, il bilanciamento del carico di un data center) e il failover di test (ad esempio la prova di un piano di ripristino).</p>
	<p>Il provider nel server VMM riceve la notifica dell'evento dal Servizio ed esegue un'azione di failover sull'host Hyper-V tramite le interfacce VMM. Il failover effettivo della macchina virtuale da un host Hyper-V a un altro (generalmente in esecuzione in un data center di "ripristino" diverso) è gestita dalla tecnologia di replica Hyper-V disponibile in Windows Server 2012 o Windows Server 2012 R2. Al termine del failover, il provider installato nel server VMM del data center di "ripristino" invia al Servizio le informazioni relative all'esito positivo dell'operazione.</p>
	</td>
	<td><p>Il Servizio usa i dati citati sopra per popolare le informazioni relative allo stato dell'azione di failover nel portale del Servizio.</p>
	</td>
	<td><p>Il Servizio usa le informazioni citate sopra nel modo seguente:</p>
		<ul>
			<li>Certificato di gestione: viene usato per identificare e autenticare il server VMM registrato per accedere al Servizio. Il servizio usa la parte della chiave pubblica del certificato per fornire un token a cui solo il server VMM registrato può accedere. Il server dovrà usare questo token per accedere alle funzionalità del Servizio.</li>
			<li>Nome del server VMM: il nome del server VMM è necessario per identificare e comunicare con il server VMM appropriato in cui si trovano i cloud. 
			</li>
			<li>Nomi dei cloud dal server VMM: il nome del cloud è obbligatorio quando si usa la funzionalità di associazione/annullamento associazione descritta di seguito. Quando si decide di associare il cloud da un data center principale con un altro cloud nel data center di ripristino, vengono presentati i nomi di tutti i cloud dal data center di ripristino.</li>
		</ul>
	</td>
	<td><p>Poiché si tratta di una parte essenziale del servizio, questa funzionalità non può essere disabilitata. Se non si vogliono inviare queste informazioni al Servizio, non usare questo Servizio.</p></td>
</tr>
</table>



<!--HONumber=49-->