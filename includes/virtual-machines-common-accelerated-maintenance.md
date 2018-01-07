

## <a name="what-is-happening"></a>Cosa sta succedendo?

Stato di una vulnerabilità di sicurezza a livello di settore, basato su hardware [divulgate il 3 gennaio](https://googleprojectzero.blogspot.com/2018/01/reading-privileged-memory-with-side.html). I clienti di mantenere protetto è sempre la priorità e si sono eseguendo i passaggi attivi per garantire che nessun cliente di Azure viene esposto a queste vulnerabilità.

Con la diffusione pubblica di vulnerabilità della sicurezza, abbiamo [accelerated l'intervallo di manutenzione pianificata](https://azure.microsoft.com/blog/securing-azure-customers-from-cpu-vulnerability/) e si è iniziato il riavvio automatico delle macchine virtuali che è ancora necessario l'aggiornamento.
 
## <a name="how-can-i-see-which-of-my-vms-are-already-updated"></a>Come è possibile vedere quali macchine virtuali vengono già aggiornate? 

È possibile visualizzare lo stato delle macchine virtuali, se il riavvio è stata completata, nel [elenco VM nel portale di Azure](https://aka.ms/T08tdc). Se l'aggiornamento è stato applicato, o "Pianificato" se l'aggiornamento è comunque necessario, le macchine virtuali sono elencate come "già aggiornato". Se si desidera visualizzare solo le macchine virtuali "Pianificato" consultare il [dell'integrità del servizio di Azure](https://portal.azure.com/).

## <a name="can-i-find-out-exactly-when-my-vms-will-be-rebooted"></a>È possibile stabilire esattamente quando macchine virtuali verranno riavviate?

Il modo migliore per ottenere un avviso per il riavvio del computer consiste nel configurare [agli eventi pianificati](https://docs.microsoft.com/azure/virtual-machines/windows/scheduled-events). Ciò fornisce una notifica di 15 minuti della macchina virtuale sta perdendo per interventi di manutenzione.

## <a name="can-i-manually-redeploy-now-to-perform-the-required-maintenance"></a>È ridistribuire manualmente ora per eseguire la manutenzione necessaria? 

È possibile garantire che una macchina virtuale di ridistribuito da allocare a un host aggiornato. Laddove possibile, l'infrastruttura di Azure tenterà di allocare le macchine virtuali agli host che sono già aggiornati. È possibile che la ridistribuzione di una macchina virtuale potrebbe inserite in un host non aggiornato, nel qual caso potrebbe essere soggetto a un secondo riavvio, forzato come parte della manutenzione pianificata. Di conseguenza, le distribuzioni manuali non sono consigliate come soluzione alternativa.

## <a name="how-long-will-the-reboot-take"></a>Il tempo il riavvio necessario? 

La maggior parte dei riavvii richiede circa **30 minuti**.

## <a name="does-the-guest-os-need-to-be-updated"></a>Se il guest OS dovranno essere aggiornati. 

L'aggiornamento dell'infrastruttura di Azure risolve la vulnerabilità divulgata a livello di hypervisor e non richiede un aggiornamento per le immagini di Windows o Linux VM. Tuttavia, come sempre, si deve continuare ad applicare le procedure consigliate per le immagini di macchina virtuale. Consultare il fornitore del sistema dei sistemi operativi per gli aggiornamenti e le istruzioni, in base alle esigenze. Per i clienti di macchina virtuale Windows Server, informazioni aggiuntive a questo punto è stato pubblicato e disponibile [qui](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV180002).

## <a name="will-there-be-a-performance-impact-as-a-result-of-resolving-this-update"></a>Vi sarà un impatto sulle prestazioni a causa di risoluzione di questo aggiornamento?

La maggior parte dei clienti di Azure non sono visibili un impatto significativo sulle prestazioni con questo aggiornamento. Si è lavorato per ottimizzare la CPU e il percorso dei / o disco e impatto significativo sulle prestazioni non viene visualizzato dopo l'applicazione per risolvere il problema. Un piccolo set di clienti potrebbero riscontrare un impatto sulle prestazioni di rete. Questo problema può essere risolto tramite Azure Accelerated rete, per [Windows](https://docs.microsoft.com/en-us/azure/virtual-network/create-vm-accelerated-networking-powershell) o [Linux](https://docs.microsoft.com/en-us/azure/virtual-network/create-vm-accelerated-networking-cli), che è una funzionalità disponibile disponibile per tutti i clienti di Azure.

## <a name="i-follow-your-recommendations-for-high-availability-will-my-environment-remain-highly-available-during-the-reboot"></a>Seguire le raccomandazioni per la disponibilità elevata, l'ambiente rimarranno a disponibilità elevata durante il riavvio?

Sì, impostare le macchine virtuali distribuite in un disponibilità oppure il set di scalabilità di macchine virtuali il costrutto di domini di aggiornamento (UD). Quando si esegue la manutenzione, Azure rispetta il vincolo dei domini di aggiornamento e non riavvia le macchine virtuali da domini di aggiornamento diversi (nello stesso set di disponibilità). Per ulteriori informazioni sulla disponibilità elevata, fare riferimento a [gestione della disponibilità delle macchine virtuali Windows in Azure](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability) o [gestione della disponibilità delle macchine virtuali Linux in Azure](https://docs.microsoft.com/azure/virtual-machines/linux/manage-availability).

## <a name="i-have-architected-my-business-continuitydisaster-recovery-plan-using-region-pairs-will-reboots-to-my-vms-occur-in-region-pairs-at-the-same-time"></a>È stato progettato il piano di ripristino di emergenza di continuità/di business utilizzando coppie di area. Riavvii di macchine virtuali si verificherà in coppie area allo stesso tempo?

In genere, gli eventi di manutenzione pianificata di Azure vengono implementati in aree abbinate uno alla volta per ridurre al minimo il rischio di interruzione in entrambe le aree. Tuttavia, a causa della natura urgente di questo aggiornamento della sicurezza, ci stiamo rollout l'aggiornamento a tutte le aree contemporaneamente.

## <a name="what-about-paas-services-on-azure"></a>Per quanto riguarda i servizi PaaS in Azure?  

I servizi della piattaforma Azure inclusi web & dispositivi mobili, dati servizi IoT, senza server, e così via sono risolti la vulnerabilità. Non vi è nessuna azione necessaria per i clienti che utilizzano questi servizi.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni, vedere [protezione di Azure i clienti da vulnerabilità CPU](https://azure.microsoft.com/blog/securing-azure-customers-from-cpu-vulnerability/).
