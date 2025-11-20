```html
<!DOCTYPE html>
<html lang="tr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Ders Notları Uygulaması</title>
    <style>
        * { box-sizing: border-box;
        body {
            font-family: "Segoe UI", Arial, sans-serif;
            background: #f7f9fc;
            color: #333;
            margin: 0;
            padding: 20px;
        }
        h1 {
            text-align: center;
            color: #2c3e50;
            margin-bottom: 30px;
        }
        #yeniDers {
            display: block;
            margin: 0 auto 30px;
            padding: 12px 30px;
            font-size: 16px;
            background: #3498e44ad;
            color: white;
            border: none;
            border-radius: 6px;
            cursor: pointer;
        }
        #yeniDers:hover { background: #732d91; }

        .ders {
            background: white;
            border-radius: 10px;
            margin-bottom: 20px;
            box-shadow: 0 4px 12px rgba(0,0,0,0.1);
            overflow: hidden;
        }
        .ders-baslik {
            background: #3498db;
            color: white;
            padding: 15px 20px;
            margin: 0;
            cursor: pointer;
            display: flex;
            justify-content: space-between;
            align-items: center;
            font-size: 18px;
        }
        .ders-baslik:hover { background: #2980b9; }
        .ders-islemler {
            display: flex;
            gap: 10px;
        }
        .btn-kucuk {
            background: rgba(255,255,255,0.2);
            color: white;
            border: none;
            padding: 6px 12px;
            border-radius: 4px;
            cursor: pointer;
            font-size: 14px;
        }
        .btn-kucuk:hover { background: rgba(255,255,255,0.4); }
        .btn-sil { background: #e74c3c !important; }
        .btn-sil:hover { background: #c73c2c !important; }

        .konular {
            padding: 0 20px 20px;
        }
        .konu {
            background: #f8f9fa;
            border: 1px solid #dee2e6;
            border-radius: 8px;
            margin-top: 15px;
            padding: 15px;
        }
        .konu-baslik {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 10px;
            font-size: 16px;
            color: #2c3e50;
        }
        textarea {
            width: 100%;
            min-height: 200px;
            padding: 12px;
            border: 1px solid #ccc;
            border-radius: 6px;
            font-family: inherit;
            font-size: 15px;
            resize: vertical;
        }
        textarea:focus {
            outline: none;
            border-color: #3498db;
            box-shadow: 0 0 0 3px rgba(52,152,219,0.2);
        }
        .bos {
            text-align: center;
            color: #95a5a6;
            font-style: italic;
            padding: 40px;
        }
    </style>
</head>
<body>
    <h1>Ders Notları Uygulaması</h1>
    <button id="yeniDers">+ Yeni Ders Ekle</button>
    <div id="dersler"></div>

    <script>
        let data = { dersler: [] };

        // localStorage'dan yükle
        const saved = localStorage.getItem('dersNotlari');
        if (saved) data = JSON.parse(saved);

        function kaydet() {
            localStorage.setItem('dersNotlari', JSON.stringify(data));
        }

        function render() {
            const container = document.getElementById('dersler');
            container.innerHTML = '';

            if (data.dersler.length === 0) {
                container.innerHTML = '<div class="bos">Henüz hiç ders eklenmemiş. Soldan "Yeni Ders Ekle" butonuna tıklayın.</div>';
                return;
            }

            data.dersler.forEach((ders, dersIndex) => {
                const dersDiv = document.createElement('div');
                dersDiv.className = 'ders';

                const baslik = document.createElement('div');
                baslik.className = 'ders-baslik';
                baslik.innerHTML = `<span>${ders.ad}</span>`;

                const islemler = document.createElement('div');
                islemler.className = 'ders-islemler';

                const btnYeniKonu = document.createElement('button');
                btnYeniKonu.className = 'btn-kucuk';
                btnYeniKonu.textContent = '+ Konu';
                btnYeniKonu.onclick = (e) => {
                    e.stopPropagation();
                    const konuAd = prompt('Yeni konu adı:');
                    if (konuAd && konuAd.trim() !== '') {
                        ders.konular.push({ ad: konuAd.trim(), not: '' });
                        kaydet();
                        render();
                    }
                };

                const btnSilDers = document.createElement('button');
                btnSilDers.className = 'btn-kucuk btn-sil';
                btnSilDers.textContent = 'Dersi Sil';
                btnSilDers.onclick = (e) => {
                    e.stopPropagation();
                    if (confirm(`"${ders.ad}" dersini tamamen silmek istediğinize emin misiniz?`)) {
                        data.dersler.splice(dersIndex, 1);
                        kaydet();
                        render();
                    }
                };

                islemler.appendChild(btnYeniKonu);
                islemler.appendChild(btnSilDers);
                baslik.appendChild(islemler);
                dersDiv.appendChild(baslik);

                const konularDiv = document.createElement('div');
                konularDiv.className = 'konular';

                if (ders.konular.length === 0) {
                    konularDiv.innerHTML = '<div class="bos">Bu derste henüz konu yok. Yukarıdaki "+ Konu" butonuna tıklayın.</div>';
                }

                ders.konular.forEach((konu, konuIndex) => {
                    const konuDiv = document.createElement('div');
                    konuDiv.className = 'konu';

                    const konuBaslik = document.createElement('div');
                    konuBaslik.className = 'konu-baslik';
                    konuBaslik.innerHTML = `<strong>${konu.ad}</strong>`;

                    const btnSilKonu = document.createElement('button');
                    btnSilKonu.className = 'btn-kucuk btn-sil';
                    btnSilKonu.textContent = 'Konuyu Sil';
                    btnSilKonu.onclick = () => {
                        if (confirm(`"${konu.ad}" konusunu silmek istediğinize emin misiniz?`)) {
                            ders.konular.splice(konuIndex, 1);
                            kaydet();
                            render();
                        }
                    };

                    konuBaslik.appendChild(btnSilKonu);

                    const textarea = document.createElement('textarea');
                    textarea.value = konu.not || '';
                    textarea.placeholder = 'Buraya notlarınızı yazın...';
                    textarea.oninput = () => {
                        konu.not = textarea.value;
                        kaydet();
                    };

                    konuDiv.appendChild(konuBaslik);
                    konuDiv.appendChild(textarea);
                    konularDiv.appendChild(konuDiv);
                });

                dersDiv.appendChild(konularDiv);
                container.appendChild(dersDiv);

                // Accordion (aç/kapa)
                baslik.onclick = () => {
                    const acikMi = konularDiv.style.display === 'block';
                    konularDiv.style.display = acikMi ? 'none' : 'block';
                };

                // İlk açılışta kapalı olsun
                konularDiv.style.display = 'none';
            });
        }

        document.getElementById('yeniDers').onclick = () => {
            const dersAd = prompt('Yeni ders adı:');
            if (dersAd && dersAd.trim() !== '') {
                data.dersler.push({ ad: dersAd.trim(), konular: [] });
                kaydet();
                render();
            }
        };

        // İlk render
        render();
    </script>
</body>
</html>
