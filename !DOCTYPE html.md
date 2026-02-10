<!DOCTYPE html>  
<html lang="tr">  
<head>  
<meta charset="UTF-8">  
<title>Okey Salonu</title>  
<meta name="viewport" content="width=device-width, initial-scale=1">  
<style>  
body { font-family: Arial; background:#111; color:white; text-align:center; }  
.grid { display:grid; grid-template-columns: repeat(3,1fr); gap:10px; margin:20px; }  
.masa { padding:20px; background:#444; border-radius:10px; font-size:18px; }  
.dolu { background:green; }  
button { padding:12px; margin:5px; font-size:16px; border-radius:8px; }  
input { padding:8px; font-size:16px; width:80px; }  
#panel, #rapor, #ayar { display:none; }  
</style>  
</head>  
<body>  
  
<div id="login">  
<h2>Giriş</h2>  
<input type="password" id="sifre" placeholder="Şifre"><br><br>  
<button onclick="giris()">GİRİŞ</button>  
</div>  
  
<div id="uygulama" style="display:none;">  
<h2>Okey Salonu</h2>  
<div id="kasa">Toplam: 0 ₺</div>  
<button onclick="raporAc()">RAPOR</button>  
<button onclick="ayarAc()">FİYAT AYARLA</button>  
  
<div class="grid" id="masalar"></div>  
  
<div id="panel">  
<h3 id="masaBaslik"></h3>  
  
<button onclick="ekle('cay')">Çay</button>  
<button onclick="ekle('meyvecayi')">Meyve Çayı</button>  
<button onclick="ekle('fusetea')">Fuse Tea</button>  
<button onclick="ekle('turkkahvesi')">Türk Kahvesi</button>  
<button onclick="ekle('kola')">Kola</button>  
<button onclick="ekle('meyvelisoda')">Meyveli Soda</button>  
<button onclick="ekle('sadesoda')">Sade Soda</button>  
  
<br><br>  
<button onclick="kapat()">MASAYI KAPAT</button>  
<button onclick="panelKapat()">GERİ</button>  
</div>  
  
<div id="rapor">  
<h3>Günlük Rapor</h3>  
<div id="raporIcerik"></div>  
<button onclick="raporKapat()">GERİ</button>  
<button onclick="gunSifirla()">GÜNÜ SIFIRLA</button>  
</div>  
  
<div id="ayar">  
<h3>Fiyat Ayarla</h3>  
Çay: <input id="fcay"><br>  
Meyve Çayı: <input id="fmeyvecayi"><br>  
Fuse Tea: <input id="ffusetea"><br>  
Türk Kahvesi: <input id="fturkkahvesi"><br>  
Kola: <input id="fkola"><br>  
Meyveli Soda: <input id="fmeyvelisoda"><br>  
Sade Soda: <input id="fsadesoda"><br><br>  
<button onclick="fiyatKaydet()">KAYDET</button>  
<button onclick="ayarKapat()">GERİ</button>  
</div>  
</div>  
  
<script>  
let sifre="1234";  
  
let fiyatlar = JSON.parse(localStorage.getItem("fiyatlar")) || {  
 cay:25, meyvecayi:35, fusetea:75, turkkahvesi:75, kola:75, meyvelisoda:55, sadesoda:45  
};  
  
let masalar = JSON.parse(localStorage.getItem("masalar")) || Array(9).fill(0);  
let kasa = parseInt(localStorage.getItem("kasa")) || 0;  
  
let satislar = JSON.parse(localStorage.getItem("satislar")) || {  
 cay:0, meyvecayi:0, fusetea:0, turkkahvesi:0, kola:0, meyvelisoda:0, sadesoda:0  
};  
  
let aktif=-1;  
  
function giris(){  
 if(document.getElementById("sifre").value==sifre){  
  document.getElementById("login").style.display="none";  
  document.getElementById("uygulama").style.display="block";  
  goster();  
 }  
}  
  
function goster(){  
 let d="";  
 masalar.forEach((m,i)=>{  
  d+=`<div class="masa ${m>0?'dolu':''}" onclick="ac(${i})">Masa ${i+1}<br>${m} ₺</div>`;  
 });  
 document.getElementById("masalar").innerHTML=d;  
 document.getElementById("kasa").innerText="Toplam: "+kasa+" ₺";  
 localStorage.setItem("masalar",JSON.stringify(masalar));  
 localStorage.setItem("kasa",kasa);  
 localStorage.setItem("satislar",JSON.stringify(satislar));  
 localStorage.setItem("fiyatlar",JSON.stringify(fiyatlar));  
}  
  
function ac(i){  
 aktif=i;  
 document.getElementById("panel").style.display="block";  
 document.getElementById("masaBaslik").innerText="Masa "+(i+1);  
}  
  
function panelKapat(){ document.getElementById("panel").style.display="none"; }  
  
function ekle(urun){  
 masalar[aktif]+=fiyatlar[urun];  
 satislar[urun]++;  
 goster();  
}  
  
function kapat(){  
 kasa+=masalar[aktif];  
 masalar[aktif]=0;  
 panelKapat();  
 goster();  
}  
  
function raporAc(){  
 document.getElementById("rapor").style.display="block";  
 let r="";  
 for(let u in satislar){  
  r+=u+" : "+satislar[u]+"<br>";  
 }  
 r+="<br>TOPLAM: "+kasa+" ₺";  
 document.getElementById("raporIcerik").innerHTML=r;  
}  
  
function raporKapat(){ document.getElementById("rapor").style.display="none"; }  
  
function gunSifirla(){  
 if(confirm("Günü sıfırlamak istiyor musun?")){  
  kasa=0;  
  for(let u in satislar) satislar[u]=0;  
  goster();  
  raporKapat();  
 }  
}  
  
function ayarAc(){  
 document.getElementById("ayar").style.display="block";  
 fcay.value=fiyatlar.cay;  
 fmeyvecayi.value=fiyatlar.meyvecayi;  
 ffusetea.value=fiyatlar.fusetea;  
 fturkkahvesi.value=fiyatlar.turkkahvesi;  
 fkola.value=fiyatlar.kola;  
 fmeyvelisoda.value=fiyatlar.meyvelisoda;  
 fsadesoda.value=fiyatlar.sadesoda;  
}  
  
function ayarKapat(){ document.getElementById("ayar").style.display="none"; }  
  
function fiyatKaydet(){  
 fiyatlar.cay=parseInt(fcay.value);  
 fiyatlar.meyvecayi=parseInt(fmeyvecayi.value);  
 fiyatlar.fusetea=parseInt(ffusetea.value);  
 fiyatlar.turkkahvesi=parseInt(fturkkahvesi.value);  
 fiyatlar.kola=parseInt(fkola.value);  
 fiyatlar.meyvelisoda=parseInt(fmeyvelisoda.value);  
 fiyatlar.sadesoda=parseInt(fsadesoda.value);  
 goster();  
 ayarKapat();  
}  
  
goster();  
</script>  
</body>  
</html>  
