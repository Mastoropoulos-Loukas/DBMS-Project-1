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
<<<<<<< HEAD
        

    HT_Init

    
        

    
    
=======
        Παίρνουμε 2 αυθεραιτες δεκαεξαδικες τιμες(0x811c9dc5 0x01000193) και για κάθε byte του id κανουμε XOR με τη 1η και πολλαπλασιάζουμε το αποτέλεσμα με τη 2η.Τέλος, κρατάμε τα depth πιο σημαντκα ψηφία.
            

    HT_Init:
        Η συνάρτηση δεν καλεί την BF_Init για την αρχικοποίηση του επιπέδου BF , με αποτέλεσμα αυτό να αποτελεί υποχρέωση του χρήστη. Στην αρχή ελέγχει αν το MAX_OPEN_FILES είναι θετικός αριθμός γιατί αλλιώς δεν μπορεί να συνεχιστεί το πρόγραμμα. Εν συνεχεία, αρχικοποιεί όλες τις θέσεις του IndexArray σαν διαθέσιμες και υπολογίζει το max_records και max_hNodes.

    HT_CreateIndex:
        Ελέγχει τις τιμές των ορισμάτων. Αν αυτές εσφαλμένες , επιστρέφει HT_ERROR. Έπειτα, δημιουργεί με την BF_CreateFile το αρχείο με όνομα filename. Αν το αρχείο filename υπάρχει ήδη τότε επιστρέφει HT_ERROR μεσω της CALL_BF. Δημιουργούμε ένα αντικείμενο Entry empty , το οποίο είναι τα δεδομένα ενός block δεδομένων που δεν έχει κάποια εγγραφή(βλ. παρακάτω). Για κάθε τιμή κατακερματισμού(σύμφωνα με το depth που δώθηκε σαν όρισμα),δημιουργούμε το κατάλληλο hashEntry(που περιέχει 2^depth hashNodes).Στο πρώτο block του αρχειου(block_num=0) αποθηκεύουμε το global depth σαν int. Αποθηκεύουμε το hashEntry στο 2 μλποκ του αρχειου(εκει ξεκιναει ο πινακας κατακερματισμου του αρχειου).Για κάθε τιμη κατακερματισμου, δεσμεύουμε ένα block δεδομένων(local depth == global depth).

    HT_OpenIndex:
        Ψαχνουμε να βρουμε μια κενη θεση στον πινακα IndexArray ελέγχοντας το πεδιο used(indexArray[i].used == 0) των IndexNode. Αν δεν βρούμε κενή θέση , επιστρέφουμε HT_ERROR, αλλιως αποθηκεύουμε τις πληροφορίες του αρχείου στο IndexArray κάνοντας used (indexArray[pos].used = 1) την θέση που βρήκαμε.

    HT_CloseFile:
        Ελέγχουμε αν το ζητούμενο αρχείο είναι ανοικτο. Αν δεν είναι, επιστρέφουμε HT_ERROR, αλλιως απελευθερώνουμε τη θέση που καταλάμβανε το αρχείο στο IndexArray κλείνοντας έπειτα το αρχείο.

    HashStatistics:
        Για καθε στατιστικο στοιχείο έχουμε μια for loop που περναει από κάθε block δεδομένων. Τα block δεδομένων βρίσκονται από τον πίνακα κατακερματισμού , οπου ελέγχει κάθε τιμή. Για να αποφύγουμε τιμέσ κατακερματισμού που δείχνουν στο ίδιο block δεδομένων και να υπολογισουμε τα στατιστικά κάθε block δεδομένων μια μόνο φορά , κάνουμε τα εξής:
            Εαν το τοπικο βάθος είναι μικροτερο απο το ολικο βάθος, τότε πολλαπλοί δείκτες από HashNode δείχνουν στο ίδιο μλοκ δεδομένων. Από τη θεωρία, οι τιμές κατακερματισμού είναι συνεχόμενες και το πληθος τους ειναι 2^dif (dif=ολικο_βαθος-τοπικο_βαθος).
            Πρσοσθέτουμε στο i (iteration counter) την παραπάνω ποσότητα - 1 για να παραλείψουμε του επόμενους δείκτες που δείχνουν στο ίδιο block που μολις εξετάσαμε.
            Αρχικοποιουμε max και total σε 0 και min σε -1 .
            Σε κάθε επανάληψη,(num=το πλήθος των εγγραφων του block)
                -Προσθέτουμε στο total το num 
                -Συγκρίνουμε το max με το num,αν num > max τοτε max=num
                -Συγκρίνουμε το min με το num ,αν num < min or min == -1 (τιμη αρχικοποιησης) τοτε min=num
                -Αφαιρουμε το 2^dif απο το dataN(πλήθος διαφορετικων block δεδομενων),γιατι εχει αρχικοποιηθει στο πληθος των τιμων κατακερματισμου.Ο λογος περιγράφεται παραπανω
            Εκτυπώνουμε τα ζητουμενα.

    HT_Insert_Entry:
        Ελέγχεται αν τα αρχείο έχει ανοικτει.Αν δεν έχει επιστρέφουμε HT_ERROR.
        Παιρνουμε απο το 1ο block αρχειου το global depth και το αποθηκευουμε στη μεταβλητη depth.
        Αποθηκευουμε στην μεταβλητη value την τιμη κατακερματισμου του id της εγγραφης που δοθηκε σαν ορισμα.
        Στην μεταβλητη hashEntry κραταμε την τρεχουσα εικονα του πινακα κατακερματισμου(η υλοποιηση μας επιτρέπει μεχρι 1 block για τον πινακα κατακερματισμου  :( ).
        Στην hashN αποθηκευουμε το πληθος των δυνατων τιμων κατακερματισμου(2^depth).
        Για κάθε HashNode του πινακα , συγκρινουμε το πεδιο value με την τιμη κατακερματισμου και όταν το βρουμε διακοπτουμε την επαναληψη.
        Αποθηκευουμε στη μεταβλητη blockN το block_num του block των δεδομενων που δειχνει το παραπανω HashNode.
        Παιρνουμε το block αυτο απο τον δισκο και αποθηκευουμε τα δεδομενα του στην μεταβλητη Entry
        Αποθηκευουμε στην μεταβλητη size το πληθος των εγγραφων του block και στη μεταβλητη locacl_depth το τοπικο του βαθος
        Εαν το block έχει το μεγιστο πληθος εγγραφων,σύμφωνα με τη θεωρια:
            -Εαν το τοπικο βαθος ειναι ισο με το ολικο ,χρειαζεται να διπλασιασουμε τον πινακα:
                ->Δημιουργουμε ενα HashEntry new στο οποιο θα αποθηκευσουμε τα δεδομενα του καινουριου πινακα κατακερματισμου.
                ->Ενημερώνουμε την κεφαλιδα για τον διπλασιασμο του μεγέθους
                ->Για καθε καινουρια(i) τιμη το αντιστοιχο HashNode του πινακα δειχνει στο block που εδειχνε το HashNode στο παλιο πινακα με τιμη i>>1 (δηλαδη με τα ιδια depth - 1 πιο σημαντικα bits)
                ->Αποθηκευουμε τα δεδομενα του καινουριου πινακα κατακερματισμου στο 2ο block του αρχειου
                ->Αυξανουμε την τιμη του ολικου βαθους και την αποθηκευουμε στο 1ο block του αρχειου.
                ->Ενημερωνουμε την τρεχουσα κατασταση του πινακα κατακερματισμου
            -Αποθηκευουμε τη διαφορα ολικο_βαθος - τοπικο_βαθος (dif=ολικο_βαθος - τοπικο_βαθος)
            -Αποθηκευουμε στη μεταβλητη numOfHashes την τιμή 2^dif (δλδ το πληθος των συνεχομενων HashNode που δειχνουνε στο block δεδομενων που μας ενδιαφερει)
            -Βρισκουμε τη 1η τιμη κατακερματισμου που δειχνει στο block δεδομενων που μας ενδιαφερει και την αποθηκευουμε στην μεταβλητη first
            -Στην μεταβλητη end αποθηκεουμε την τελευταια τιμη κατακερματισμου που δειχνει στο block δεδομενων που μας ενδιαφερει
            -Στην μεταβλητη half αποθηκευουμε το μεσο του διαστηματος [first,end] που μας ενδιαφερει
            -Δεσμευουμε ενα καινουργιο block και αποθηκευουμε στην μεταβλητη blockNew το block_num του(καλουμε την BF_GetBlockCounter πριν δεσμεύσουμε καινουργιο block)
            -Ορίζουμε 2 δομές Entry ,τις old και new. H old θα περιεχει τις εγγραφες που θα παραμεινουν απο το παλιο μπλοκ και η new θα περιεχει τις εγγραφες που θα πανε στο καινουργιο block που μολις δεσμευσαμε.
            -Θέτουμε και στις 2 τοπικο βαθος = local_depth + 1 και size=0
            -Τα hashNode με τιμες κατακερματισμου απο first μεχρι half  δειχνουν στο παλιο block (δεν χρειαζεται κωδικα γιατι ουτως ή αλλως εκει εδειχναν) 
            -Θετουμε τα hashNode με τιμες κατακερματισμου απο half+1 μεχρι end να δειχνουν στο παλιο block(εκχωρουμε στο πεδιο block_num τους την τιμη του blockNew).
            -Αποθηκευουμε τα δεδομενα του πινακα κατακερματισμου στο 2ο block του αρχειου
            -Για καθε εγγραφη που είχαμε και την καινουργια που βαζουμε αν η τιμη κατακερματισμου του id της ειναι απο fisrt μεχρι half την βαζουμε στο παλιο block αλλιως στο καινουργιο
            -Γραφουμε τα 2 block στο δισκο
            -Τερματιζουμε επιστρεφοντας HT_OK
        Αν δεν εχουμε τερματισει(δλδ εχουμε χωρο στο block δεδομενων με block_num = blockN) παιρνουμε το block,του βάζουμε την εγγραφή ,του αυξάνουμε το πεδίο size της κεφαλίδας,γραφουμε το block στη μνημη και τερματιζουμε     

    HT_PrintAllEntries:
        Ελέχγουμε αν το αρχείο είναι ανοικτό.Αν δεν είναι επιστρέφουμε HT_ERROR.
        Παιρνουμε απο το 1ο block του αρχειου το ολικο βαθος και το αποθηκευουμε στην μεταβλητη depth.
        Αποθηκευουμε στη μεταβλητη hashN την τιμη 2^depth (το πληθος των τιμων κατακερματισμου)
        Αποθηκευουμε στη μεταβλητη hashEntry τα δεδομενα του πινακα κατακερματισμου που τα παιρνουμε απο το 2ο block του αρχειου
        Εαν έχει δωθει σαν id τιμη NULL,εκτυπωνουμε ολες τις εγγραφες:
            -Για καθε hashNode του πινακα κατακερματισμου:
                Κραταμε στην μεταβλητη blockΝ τον αριθμο block του αντιστοιχου block δεδομενων
                Παιρνουμε αυτο το block και εκτυπωνουμε ολες τις εγγραφες του
                Παραλειπουμε τυχον hashNodes που δειχνουν στο ιδιο block με τον τροπο που αναφερθηκε στην HT_InsertEntry
                Τερματιζουμε επιστρεφοντας HT_OK
        Εαν δεν εχουμε τερματισει (δλδ ψαχνουμε εγγραφες με συγκεκριμενο id):
            Αποθηκευουμε την τιμη κατακερματισμου του id στην μεταβλητη value
            Αποθηκευουμε το πεδιο block_num , του ΗashNode με τιμη κατακερματισμου value,στην μεταβλητη blockN
            Εαν δεν εχει δεσμευτει block(δλδ blockN==0) τερματιζουμε επιστρεφοντας HT_ERROR
            Παιρνουμε το σχετικο block
            Εκτυπώνουμε καθε εγγραφη με το ζητουμρ id

ΔΥΣΛΕΙΤΟΥΡΓΙΕΣ:
    Ο κώδικας δεν δυναται να αξιοποιει πινακα κατακακερματισμου μεγαλυτερο απο οτι χωραει σε 1 block (δλδ για μεγαλυτερο απο 62 HashNodes)
    Αυτο έχει σαν αποτέλεσμα το πρόγραμμα να διαχειρίζεται περιορισμενο πληθος εγγραφων. Με τον συγκεκριμενο τρόπο δημιουργιας εγγραφων στην ht_main και με το συγκεκριμενο seed στην srand, το πληθος αυτο ειναι 277 εγγραφες.              
>>>>>>> 01062e35378b2cccb01dbdd0d3e83233d1bbe9ad
