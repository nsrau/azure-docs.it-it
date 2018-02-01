

## <a name="what-is-happening"></a>Cosa sta succedendo?

[Il 3 gennaio è stata diffusa una notizia](https://googleprojectzero.blogspot.com/2018/01/reading-privileged-memory-with-side.html) riguardante una vulnerabilità di sicurezza basata su hardware e che interessa l'intero settore. La sicurezza dei clienti è sempre la priorità assoluta di Microsoft, che si sta impegnando attivamente per garantire che i clienti di Azure non siano esposti a queste vulnerabilità.

A causa della pubblica divulgazione delle vulnerabilità di protezione, Microsoft [ha accelerato le tempistiche della manutenzione pianificata](https://azure.microsoft.com/blog/securing-azure-customers-from-cpu-vulnerability/) e ha iniziato il riavvio automatico delle macchine virtuali che dovevano ancora essere aggiornate.


## <a name="how-can-i-see-which-of-my-vms-are-already-updated"></a>Come è possibile controllare quali macchine virtuali sono già state aggiornate? 

È possibile visualizzare lo stato delle macchine virtuali e verificare se è stato completato il riavvio nell'[elenco delle macchine virtuali nel portale di Azure](https://aka.ms/T08tdc). Le macchine virtuali verranno elencate come "Già aggiornato" se l'aggiornamento è stato applicato o come "Pianificato" se l'aggiornamento è ancora richiesto. Se si desidera visualizzare solo le macchine virtuali elencate come "Pianificato", consultare l'[integrità dei servizi di Azure](https://portal.azure.com/).

## <a name="can-i-find-out-exactly-when-my-vms-will-be-rebooted"></a>È possibile capire esattamente quando le macchine virtuali verranno riavviate?

Il modo migliore per essere avvisati del riavvio è configurare gli [eventi pianificati](https://docs.microsoft.com/azure/virtual-machines/windows/scheduled-events). In questo modo 15 minuti prima che la macchina virtuale venga portata offline per la manutenzione si riceverà una notifica.

## <a name="can-i-manually-redeploy-now-to-perform-the-required-maintenance"></a>In questo momento è possibile ridistribuire manualmente per eseguire la manutenzione necessaria? 

Microsoft non garantisce che una macchina virtuale ridistribuita verrà allocata in un host aggiornato. Laddove possibile l'infrastruttura di Azure proverà ad allocare le macchine virtuali in host già aggiornati. È possibile che la ridistribuzione di una macchina virtuale porti a un host non aggiornato. In questo caso la manutenzione pianificata potrebbe prevedere un secondo riavvio forzato. Di conseguenza le distribuzioni manuali non sono consigliate come soluzione alternativa.

## <a name="how-long-will-the-reboot-take"></a>Quanto tempo impiegherà il riavvio? 

La maggior parte dei riavvii richiede circa **30 minuti**.

## <a name="does-the-guest-os-need-to-be-updated"></a>È necessario aggiornare il sistema operativo guest? 

L'aggiornamento dell'infrastruttura di Azure risolve la vulnerabilità scoperta a livello di hypervisor e non richiede un aggiornamento delle immagini delle macchine virtuali Windows o Linux. Tuttavia si consiglia di continuare ad applicare come sempre le procedure consigliate di sicurezza per le immagini della macchina virtuale. Consultare il fornitore dei sistemi operativi per aggiornamenti e istruzioni in base alle esigenze. Per i clienti che usano macchine virtuali Windows Server sono disponibili indicazioni [qui](../articles/virtual-machines/windows/mitigate-se.md).

## <a name="will-there-be-a-performance-impact-as-a-result-of-resolving-this-update"></a>Ci saranno impatti sulle prestazioni a causa di questa risoluzione dell'aggiornamento?

La maggior parte dei clienti di Azure non ha notato impatti rilevanti sulle prestazioni in seguito all'aggiornamento. Microsoft ha lavorato per ottimizzare i percorsi di CPU e I/O del disco e non ha riscontrato particolari differenze nelle prestazioni dopo l'applicazione della correzione. Un numero ristretto di clienti potrà notare un impatto sulle prestazioni di rete. Il problema è risolvibile tramite la rete accelerata di Azure per [Windows](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell) o [Linux](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli), una funzionalità gratuita disponibile per tutti i clienti di Azure.

## <a name="i-follow-your-recommendations-for-high-availability-will-my-environment-remain-highly-available-during-the-reboot"></a>Ho seguito le raccomandazioni per la disponibilità elevata. L'ambiente avrà disponibilità elevata durante il riavvio?

Sì, le macchine virtuali distribuite in un set di disponibilità o nei set di scalabilità di macchine virtuali hanno il costrutto dei domini di aggiornamento. Quando si esegue la manutenzione, Azure rispetta il vincolo dei domini di aggiornamento e non riavvia le macchine virtuali da domini di aggiornamento diversi (nello stesso set di disponibilità). Per altre informazioni sulla disponibilità elevata, vedere [Gestire la disponibilità delle macchine virtuali Windows in Azure](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability) o [Gestire la disponibilità delle macchine virtuali Linux](https://docs.microsoft.com/azure/virtual-machines/linux/manage-availability).

## <a name="i-have-architected-my-business-continuitydisaster-recovery-plan-using-region-pairs-will-reboots-to-my-vms-occur-in-region-pairs-at-the-same-time"></a>È stato progettato un piano di continuità aziendale/ripristino di emergenza che usa coppie di aree. I riavvii delle macchine virtuali avranno luogo nelle coppie di aree nello stesso momento?

In genere, gli eventi di manutenzione pianificata di Azure vengono implementati uno alla volta nelle coppie di aree per ridurre al minimo i rischi di interruzione in entrambe le aree. Tuttavia a causa dell'urgenza dell'aggiornamento della sicurezza verrà implementato contemporaneamente in tutte le aree.

## <a name="what-about-paas-services-on-azure"></a>Cosa succede ai servizi PaaS in Azure?  

I servizi della piattaforma Azure che includono Web e dispositivi mobili, servizi dati, IoT, assenza di server e così via hanno risolto la vulnerabilità. Non sono necessarie azioni da parte dei clienti che usano questi servizi.

## <a name="intel-released-additional-guidance-on-january-22-2018-related-to-the-security-vulnerabilities--will-this-guidance-cause-any-additional-maintenance-activities-by-azure"></a>Il 22 gennaio 2018 Intel ha rilasciato del materiale sussidiario aggiuntivo sulle vulnerabilità di protezione.  Questo materiale sussidiario avvierà attività di manutenzione aggiuntive di Azure?  

Le misure di mitigazione dei rischi di Azure annunciate in precedenza, il 3 gennaio 2018 non sono interessate dal [materiale sussidiario aggiornato](https://newsroom.intel.com/news/root-cause-of-reboot-issue-identified-updated-guidance-for-customers-and-partners/) di Intel. Non sono previste attività di manutenzione aggiuntive sulle macchine virtuali dei clienti in seguito a queste nuove informazioni.
 

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere [Securing Azure customers from CPU vulnerability](https://azure.microsoft.com/blog/securing-azure-customers-from-cpu-vulnerability/) (Protezione dei clienti di Azure dalla vulnerabilità della CPU).
