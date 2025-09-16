# Apache Solr 9+ Cheat Sheet (Markdown)

## 1️⃣ Core / Collection Management

| Endpoint | Method | Contoh Request | Fungsi |
|----------|--------|----------------|--------|
| `/solr/admin/cores?action=STATUS` | GET | `curl http://localhost:8983/solr/admin/cores?action=STATUS` | Menampilkan status semua core |
| `/solr/admin/cores?action=CREATE&name=mycore&configSet=_default` | GET | `curl "http://localhost:8983/solr/admin/cores?action=CREATE&name=mycore&configSet=_default"` | Membuat core baru |
| `/solr/admin/cores?action=RELOAD&core=mycore` | GET | `curl "http://localhost:8983/solr/admin/cores?action=RELOAD&core=mycore"` | Reload core |
| `/solr/admin/cores?action=UNLOAD&core=mycore` | GET | `curl "http://localhost:8983/solr/admin/cores?action=UNLOAD&core=mycore"` | Hapus core |
| `/solr/admin/cores?action=SWAP&core=core1&other=core2` | GET | `curl "http://localhost:8983/solr/admin/cores?action=SWAP&core=core1&other=core2"` | Swap core (blue/green deployment) |

---

## 2️⃣ Indexing / Update Documents

| Endpoint | Method | Contoh Request | Fungsi |
|----------|--------|----------------|--------|
| `/solr/mycore/update` | POST | ```bash curl http://localhost:8983/solr/mycore/update -H 'Content-Type: application/json' -d '[{"id":"1","title":"Mobil Listrik 2025"}]'``` | Insert / update dokumen |
| `/solr/mycore/update?commit=true` | POST | Commit otomatis | Commit perubahan |
| `/solr/mycore/update/json/docs` | POST | ```bash curl http://localhost:8983/solr/mycore/update/json/docs -H 'Content-Type: application/json' -d '{"id":"2","title":"Kendaraan Ramah Lingkungan"}'``` | Insert dokumen JSON satu per satu |
| `/solr/mycore/update?optimize=true` | POST | ```bash curl "http://localhost:8983/solr/mycore/update?optimize=true"``` | Optimasi index |

---

## 3️⃣ Query / Search (Keyword Search)

| Endpoint | Method | Contoh Request | Fungsi |
|----------|--------|----------------|--------|
| `/solr/mycore/select?q=mobil+listrik` | GET | `curl "http://localhost:8983/solr/mycore/select?q=mobil+listrik"` | Keyword search standar |
| `/solr/mycore/select?q=mobil+listrik&fl=id,title` | GET | Ambil field tertentu | Field selection |
| `/solr/mycore/select?q=mobil+listrik&start=0&rows=10` | GET | Pagination | Mulai dari doc 0, 10 hasil |
| `/solr/mycore/select?q=mobil+listrik&fq=year:2025` | GET | Filter query | Filter hasil berdasarkan field |
| `/solr/mycore/select?q=mobil+listrik&sort=title+asc` | GET | Sorting hasil | Sort field tertentu |
| `/solr/mycore/select?q=mobil+listrik&facet=true&facet.field=brand` | GET | Faceting / aggregate | Hitung jumlah dokumen per brand |
| `/solr/mycore/select?q=mobil+listrik&hl=true&hl.fl=title,description` | GET | Highlight kata yang cocok | Highlight search result |

---

## 4️⃣ Neural / Vector Search (Solr 9+)

| Endpoint | Method | Contoh Request | Fungsi |
|----------|--------|----------------|--------|
| `/solr/mycore/select` | POST | ```bash curl http://localhost:8983/solr/mycore/select -H 'Content-Type: application/json' -d '{"query":"*:*","knn":{"field":"embedding","vector":[0.1,0.2,0.3,...,0.768],"k":10}}'``` | KNN / vector search (Neural Search) |

**Contoh Response:**
```json
{
  "response": {
    "numFound": 3,
    "docs": [
      {"id":"1","title":"Mobil Listrik Terbaru","score":0.92},
      {"id":"2","title":"Kendaraan Listrik Murah","score":0.89},
      {"id":"5","title":"Mobil Ramah Lingkungan 2025","score":0.85}
    ]
  }
}
