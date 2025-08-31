import customtkinter as ct

ct.set_appearance_mode("light")
ct.set_default_color_theme("green")

window = ct.CTk()
window.geometry("600x700")
window.title("Lukoil və Store")

basliq = ct.CTkLabel(window, text="LUKOIL & STORE", text_color="white", bg_color="blue", font=("Arial", 24, "bold"))
basliq.pack(pady=10, fill="x")

yanacaqQiymetleri = {
    "Benzin-A95": 1.60,
    "Dizel": 1.10,
    "Gaz": 0.80
}

magazaQiymetleri = {
    "Starbucks": 4.50,
    "Coca-Cola": 0.90,
    "Bulku": 1.50,
    "Piroq": 2.00
}

yanacaqFrame = ct.CTkFrame(window, fg_color="blue")
yanacaqFrame.pack(pady=10, padx=10, fill="x", anchor="w")

secilenYanacaq = ct.StringVar(value="Benzin-A95")

ct.CTkLabel(yanacaqFrame, text="Yanacaq secin:", text_color="white").pack(anchor="w", padx=10, pady=5)
for yanacaq in yanacaqQiymetleri:
    ct.CTkRadioButton(
        yanacaqFrame,
        text=f"{yanacaq} ({yanacaqQiymetleri[yanacaq]} AZN)",
        variable=secilenYanacaq,
        value=yanacaq,
        text_color="white"
    ).pack(anchor="w", padx=20)

litrSayi = ct.CTkEntry(yanacaqFrame, placeholder_text="Litr sayini (reqem) daxil edin")
litrSayi.pack(padx=20, pady=5)

yanacaqNeticesi = ct.CTkLabel(yanacaqFrame, text="Litr daxil edin ", text_color="red")
yanacaqNeticesi.pack(pady=5)

def yanacaqHesabla():
    daxilEdilen = litrSayi.get()
    if daxilEdilen.replace(".", "", 1).isdigit():
        litr = float(daxilEdilen)
        qiymet = yanacaqQiymetleri.get(secilenYanacaq.get(), 0)
        cemi = litr * qiymet
        yanacaqNeticesi.configure(text=f"Lukoil borcunuz: {cemi:.2f} AZN")
        global lukoilBorcu
        lukoilBorcu = cemi
    else:
        yanacaqNeticesi.configure(text="Xeta!!! Zehmet olmasa duzgun reqem daxil eidn.")

ct.CTkButton(yanacaqFrame, text="Hesabla", command=yanacaqHesabla).pack(pady=5)

magazaFrame = ct.CTkFrame(window, fg_color="blue")
magazaFrame.pack(pady=10, padx=10, fill="x", anchor="w")

ct.CTkLabel(magazaFrame, text="Magaza mehsullari:", text_color="white").pack(anchor="w", padx=10, pady=5)

magazaSayilari = {}
for mehsul in magazaQiymetleri:
    sira = ct.CTkFrame(magazaFrame, fg_color="white")
    sira.pack(anchor="w", padx=20, pady=2)
    ct.CTkLabel(sira, text=f"{mehsul} ({magazaQiymetleri[mehsul]} AZN)", width=150, anchor="w").pack(side="left")
    daxilEt = ct.CTkEntry(sira, placeholder_text="Say")
    daxilEt.pack(side="left")
    magazaSayilari[mehsul] = daxilEt

magazaNeticesi = ct.CTkLabel(magazaFrame, text="", text_color="red")
magazaNeticesi.pack(pady=5)

def magazaHesabla():
    cemi = 0
    xetaVar = False
    for mehsul, daxilEt in magazaSayilari.items():
        say = daxilEt.get()
        if say:
            if say.isdigit():
                cemi += int(say) * magazaQiymetleri[mehsul]
            else:
                xetaVar = True
                break
    if xetaVar:
        magazaNeticesi.configure(text="Xeta!! Sayi Duzgun(reqem) daxil edin.")
    else:
        magazaNeticesi.configure(text=f"Store borcunuz: {cemi:.2f} AZN")
        global magazaBorcu
        magazaBorcu = cemi

ct.CTkButton(magazaFrame, text="Magaza borcu", command=magazaHesabla).pack(pady=5)

umumiNetice = ct.CTkLabel(window, text="", text_color="red", font=("Arial", 16))
umumiNetice.pack(pady=10)

def umumiBorcHesabla():
    cemi = lukoilBorcu + magazaBorcu
    umumiNetice.configure(text=f"Umumi borc: {cemi:.2f} AZN\nXoş yol (: ")

ct.CTkButton(window, text="Umumi borc", command=umumiBorcHesabla).pack(pady=10)

lukoilBorcu = 0
magazaBorcu = 0

window.mainloop()

