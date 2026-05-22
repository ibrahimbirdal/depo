import sqlite3
from datetime import datetime, timedelta
import json


kullanicilar = {}

def veritabanikur():
    
    baglanti = sqlite3.connect("spor_salonu.db")
    imlec = baglanti.cursor()
    # Kullanıcı verilerini tutulacak tabloyu oluştuma
    imlec.execute('''
        CREATE TABLE IF NOT EXISTS uyeler (
            ad TEXT PRIMARY KEY,
            sifre TEXT,
            paket TEXT,
            bitis_tarihi TEXT,
            gorevler TEXT
        )
    ''')
    baglanti.commit()
    
    # Veritabanındaki kayıtları alıp sözlüğe yükleme 
    imlec.execute("SELECT * FROM uyeler")
    for satir in imlec.fetchall():
        ad, sifre, paket, bitis, gorevler = satir
        # Sözlük içerisinde tüm bilgileri tutma
        kullanicilar[ad] = {
            "sifre": sifre,
            "paket": paket,
            "bitis_tarihi": bitis,
            "gorevler": json.loads(gorevler) 
        }
    baglanti.close()

def verikaydet(ad):
   
    baglanti = sqlite3.connect("spor_salonu.db")
    imlec = baglanti.cursor()
    kisi = kullanicilar[ad]
    gorevlermetni = json.dumps(kisi["gorevler"]) 
    
    
    imlec.execute('''
        REPLACE INTO uyeler (ad, sifre, paket, bitis_tarihi, gorevler)
        VALUES (?, ?, ?, ?, ?)
    ''', (ad, kisi["sifre"], kisi["paket"], kisi["bitis_tarihi"], gorevlermetni))
    
    baglanti.commit()
    baglanti.close()


veritabanikur()


def adminpanel():
    truepassword="1245"
    print("admin menüsü")
    for sifresayac in range(3,0,-1):
        print(f"kalan deneme hakkı: {sifresayac}")
        password=input("şifreyi gir: ")
        
        if(password!=truepassword):
             
             if(sifresayac==1): 
                print("3 kere yanlış girdin bye ")
                break
             else:   
                 continue
        else:
             while True:
                print("\n-- ADMİN EKRANI --")
                print("1. Tüm Kullanıcıları Listele")
                print("2. Kullanıcıya Gün Ekleme/Çıkarma")
                print("3. Kullanıcıya Görev Atama")
                print("4. Ana Menüye Dön (Çıkış)")
                
                secim = input("Bir işlem seçiniz: ")
                
                if secim == "1":
                    print("Kullanıcılar listeleniyor...\n")
                    if not kullanicilar:
                        print("henüz kullanici yok")
                    else:
                        for ad, bilgiler in kullanicilar.items():
                            
                            print(f"Üye: {ad} | Paket: {bilgiler['paket']} | Bitiş: {bilgiler['bitis_tarihi']} | Görevler: {bilgiler['gorevler']}")
                
                elif secim == "2":
                  
                    hedef = input("İşlem yapılacak üyenin adını girin: ")
                    if hedef in kullanicilar:
                        islem = input("Gün Ekle (E) / Çıkar (C): ").upper()
                        gun = int(input("Kaç gün?: "))
                    
                        mevcutbitis = datetime.strptime(kullanicilar[hedef]["bitis_tarihi"], "%Y-%m-%d")
                        
                        if islem == 'E':
                            yenibitis = mevcutbitis + timedelta(days=gun)
                        elif islem == 'C':
                            yenibitis = mevcutbitis - timedelta(days=gun)
                            
                       
                        kullanicilar[hedef]["bitis_tarihi"] = yenibitis.strftime("%Y-%m-%d")
                        verikaydet(hedef)
                        print("Tarih başarıyla güncellendi.")
                    else:
                        print("Kullanıcı bulunamadı.")
                
                elif secim == "3":
                    
                    hedef = input("Görev atanacak üye adı: ")
                    if hedef in kullanicilar:
                        yenigorev = input("Atanacak görevi yazın: ")
                        kullanicilar[hedef]["gorevler"].append(yenigorev)
                        verikaydet(hedef)
                        print("Görev başarıyla atandı.")
                    else:
                        print("Kullanıcı bulunamadı.")
                        
                elif secim == "4":
                    print("Admin panelinden çıkılıyor. Ana menüye dönülüyor...")
                    break 
                else:
                    print("hatalı seçim yaptınızz.")
             break 

def kullanicipanel():    
    while True:
        print("\n--- KULLANICI EKRANI ---")
        ad=input("ad gir: ")
        
        if ad not in kullanicilar:
            print(f"{ad} isminde kullanici yok. yeni kayıt oluşturuluyor.")
            sifre=input("sifre gir: ")
            
            
            print("1. Aylık (30 Gün) / 2. 3 Aylık (90 Gün) / 3. Yıllık (365 Gün)")
            pktsecim = input("Paket seçiniz (1/2/3): ")
            gun = 30 if pktsecim == "1" else 90 if pktsecim == "2" else 365
            paketadi = "Aylık" if pktsecim == "1" else "3 Aylık" if pktsecim == "2" else "Yıllık"
            
            bitiszamani = datetime.now() + timedelta(days=gun)
            
            
            kullanicilar[ad] = {
                "sifre": sifre,
                "paket": paketadi,
                "bitis_tarihi": bitiszamani.strftime("%Y-%m-%d"),
                "gorevler": []
            }
            verikaydet(ad) 
            print("kayıt tamamlandı menüye geçiyoruz\n")
            
        else:
            
            girilensifre = input("sifre gir: ")
            if kullanicilar[ad]["sifre"] != girilensifre:
                print("Hatalı şifre.. Lütfen tekrar deneyin.")
                continue 

        
        while True:
            print("\n1. Paket Bilgilerini ve Kalan Günü Gör")
            print("2. Atanan Görevleri Gör/Tamamla")
            print("3. Üst Menüye Dön (Çıkış)")
            
            secim = input("Bir işlem seçiniz: ")
            
            if secim == "1":
              
                bitistarihi = datetime.strptime(kullanicilar[ad]["bitis_tarihi"], "%Y-%m-%d")
                kalangun = (bitistarihi - datetime.now()).days
                print("\n--- Profiliniz ---")
                print(f"Paket: {kullanicilar[ad]['paket']}")
                print(f"Bitiş Tarihi: {kullanicilar[ad]['bitis_tarihi']}")
                print(f"Kalan gün: {kalangun}")
                
            elif secim == "2":
                
                print("\n--- Atanan Görevler ---")
                gorevler = kullanicilar[ad]["gorevler"]
                if not gorevler:
                    print("Henüz size atanmış bir görev yok.")
                else:
                    for i, gorev in enumerate(gorevler):
                        print(f"{i+1}. {gorev}")
                        
            elif secim == "3":
                print("Kullanıcı panelinden çıkılıyor...")
                break 
            else:
                print("Yapım aşamasında veya hatalı seçim.")
        break 

while True:
    rol=input("secim yap admin user çıkıs(c): ").lower()
    if(rol=="admin"):
        adminpanel()

    elif(rol=="user"):
        kullanicipanel()
        
    elif(rol=="c"):
        break
    else:
        print("hatalı tuslama tekrar dene")
        continue
