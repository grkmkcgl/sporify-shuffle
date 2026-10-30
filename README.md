# Beğenilenleri Karıştır

Tek dosyalık (`index.html`) statik web uygulaması. Kullanıcının Spotify'da beğendiği tüm şarkıları çeker, rastgele karıştırır ve yeni bir özel çalma listesine ekler. Derleme adımı ve bağımlılık yok; saf HTML/CSS/JS.

## Çalışma şekli
- Giriş: Authorization Code + PKCE (client secret yok). Client ID sayfadan girilir, localStorage'da tutulur.
- Redirect URI: sayfanın kendi adresi (`location.origin + location.pathname`). Spotify dashboard'undaki değerle birebir aynı olmalı.
- Scope'lar: `user-library-read playlist-modify-private playlist-modify-public`
- Akış: `GET /me/tracks` (50'şerli sayfalama) → Fisher-Yates karıştırma (`crypto.getRandomValues`) → `POST /me/playlists` → `POST /playlists/{id}/items` (100'erli gruplar).
- 429 için `Retry-After` kadar bekleyip yeniden dener; 401'de token yenilemeyi dener.

## Spotify API kısıtları (Şubat 2026 sonrası, Development Mode)
- `POST /users/{id}/playlists` kaldırıldı → `POST /me/playlists` kullan.
- `/playlists/{id}/tracks` → `/playlists/{id}/items` olarak yeniden adlandırıldı. Yanıtta `tracks` alanı `items` oldu.
- Toplu getirme uçları (`GET /tracks`, `/albums`, `/artists`) kaldırıldı.
- Arama `limit` en fazla 10.
- Uygulama sahibinin Premium'u olmalı; Development Mode'da yalnızca User Management'a eklenen kullanıcılar giriş yapabilir.
- Kaynak: https://developer.spotify.com/documentation/web-api/tutorials/february-2026-migration-guide

## Yerelde çalıştırma / yayınlama
- Spotify https (veya http://127.0.0.1) redirect ister. Yerelde: `python3 -m http.server 8080` ve Redirect URI olarak `http://127.0.0.1:8080/` ekle (`localhost` kabul edilmez).
- Yayın: GitHub Pages (Settings → Pages), Redirect URI = yayın adresi.

## Fikirler
- Haftalık otomatik karışık liste
- Rastgele N şarkı seçeneği
- Mevcut bir listeyi yerinde karıştırma
