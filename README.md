# ΕΡΓΑΣΙΑ 1 ΣΤΟ ΜΑΘΗΜΑ ΥΣΒΔ

Μέλη ομάδας:
* Λινάρδος Αλέξανδρος - 1115201600093
* Μαστορόπουλος Λουκάς - 1115201700078
* Χαρίσης Νικόλαος - 1115201700187


ΕΙΣΑΓΩΓΙΚΕΣ ΣΗΜΕΙΩΣΕΙΣ:
    Στο αρχείο hash_file.h έχει προστεθεί το πρώτυπο της συνάρτησης HashStatistics() που ζητείται στην εκφώνηση. Η υλοποίησή της βρίσκεται στο hash_file.c , το οποίο περιέχει εκτός από τις ζητούμενες συναρτήσεις και αλλες 2 βοηθητικές συναρτήσεις που χρησιμεύουν στην εκτύπωση διαφόρων τύπων(printRecord και printHashNode) καθώς και μια συνάρτηση κατακερματσμού(hashFunction). Στο αρχείο ht_main.c έχει προστεθεί και μια κλήση της hashStatistcs έπειτα από τον δοσμένο κώδικα. Έχουν υλοποίηθεί όλες οι συναρτησείς στο hash_file.c  με τη χρήση διάφορων δομών , για τις οποίες ακολουθεί επιπλέον επεξήγηση. Τέλος, στο Makefile έχει προστεθεί και ενα recipe για το clean, που διαγράφει το εκτελέσιμο runner καθώς και όλα τα .db αρχεία.

ΔΟΜΕΣ ΔΕΔΟΜΕΝΩΝ ΠΟΥ ΧΡΗΣΙΜΟΠΟΙΟΥΝΤΑΙ:
    IndexNode:
        Δομή που περιέχει πληροφορίες για κάθε ανοικτό αρχείο.Το πεδιο used ειναι 0 αν ειναι ελεύθερη η θεση στον πίνακα, 1 αν δεν είναι. Το fd ειναι to file_desc που επιστρέφει η BF_OpenFile.
    
    DataHeader:
        Δομή που υλοποιεί κεφάλιδα σε μπλοκ που περιέχουν εγγραφές. Το πεδίο local_depth είναι το τοπικό βάθος, και το size το πλήθος των εγγραφών στο block

    Entry:
        Δομή που περιγράφει τα τμήματα σε ένα block δεδομένων. To πεδίο header είναι η κεφαλίδα, και ο record είναι ένας πίνανκας εγγραφών, μεγέθους όσο το μέγιστο πλήθος εγγραφών που χωράνε στο μπλοκ.

    Το μέγιστο πλήθος εγγραφών το υπολογίζουμε όπως παρουσιάστηκε στις διαλέξεις: 
    κάτω ακέραιο μέρος ((μέγεθος μπλοκ - μέγεθος κεφαλίδας) / μέγεθος εγγραφής))
    
    HashHeader:
        Δομή που υλοποιεί κεφάλιδα σε μπλοκ που περιέχουν ζεύγη (τιμής κατακερματισμού, δείκτη σε block δεδομένων). Το πεδίο size το πλήθος των HashNode στο block

    HashNode:
        Δομή που υλοποιεί ένα ζεύγος τιμής κατακερματισμού, δείκτη σε block. Η τιμή κατακερματισμού είναι ακέραιος 32 bit, ο δείκτης σε block είναι ακέραιος που περιέχει το block_num του block που δείχνει στο αρχείο.
    
    HashEntry:
        Δομή που περιγράφει τα τμήματα σε ένα block με HashNode. To πεδίο header είναι η κεφαλίδα, και ο hashNode είναι ένας πίνανκας HashNode, μεγέθους όσο το μέγιστο πλήθος HashNode που χωράνε στο μπλοκ.

    Το μέγιστο πλήθος HashNode το υπολογίζουμε παρόμοια με το μέγιστο πλήθος εγγραφών σε block: 
    κάτω ακέραιο μέρος ((μέγεθος μπλοκ - μέγεθος κεφαλίδας) / μέγεθος HashNode))

GLOBAL ΜΕΤΑΒΛΗΤΕΣ:
    max_records:
        Μεταβλητή που περιέχει το μέγιστο πλήθος εγγραφών σε ένα block. Υπολογίζεται όπως αναφέρθηκε παραπάνω.
    
    max_hNodes:
        Μεταβλητή που περιέχει το μέγιστο πλήθος HashNode σε ένα block. Υπολογίζεται όπως αναφέρθηκε παραπάνω.

    indexArray:
        Πίνακας που περιέχει πληροφορίες για όλα τα ανοικτά αρχεία όπως ζητείται στην εκφώνηση. Αν ένα αρχείο έχει ανοίξει παραπάνω από μία φορά, αντιμετωπίζεται σαν πολλά διαφορετικά ανοικτά αρχεία, με διαφορετικά indexDesc. To μέγεθος του πίνακα είναι MAX_OPEN_FILES, όσο και το μέγιστο δυνατό πλήθος ανοικτών αρχείων. 
    
ΣΥΝΑΡΤΗΣΕΙΣ:

    printRecord:
        Εκτυπώνει τα πεδία μίας εγγραφής στο standar output.
        record: η εγγραφή της οποίας τα πεδία εκτυπώνονται

    printHashNode:
        Εκτυπώνει τα πεδία ενός HashNode στο standar output.
        node: το HashNode του οποίου τα πεδία εκτυπώνονται

    hashFunction:
        Επιστρέφει, ανάλογα με το τρέχον global depth, τα πλέον σημαντικά bit της τιμής κατακερματισμού του id μιας εγγραφής.
        id : το id της εγγραφής
        depth : το τρέχον global depth
        

    HT_Init

    
        

    
    
