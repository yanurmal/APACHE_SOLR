# 📘 Apache Solr 9+ Complete API Cheat Sheet

## 1️⃣ Core / Collection Management

### 1.1 Core Management (v1 API)
| Endpoint | Method | Contoh Request | Deskripsi |
|----------|--------|----------------|-----------|
| `/solr/admin/cores?action=CREATE&name=mycore&configSet=_default` | GET | `curl "http://localhost:8983/solr/admin/cores?action=CREATE&name=mycore&configSet=_default"` | Membuat core baru |
| `/solr/admin/cores?action=RELOAD&core=mycore` | GET | `curl "http://localhost:8983/solr/admin/cores?action=RELOAD&core=mycore"` | Reload core |
| `/solr/admin/cores?action=UNLOAD&core=mycore` | GET | `curl "http://localhost:8983/solr/admin/cores?action=UNLOAD&core=mycore"` | Menghapus core |
| `/solr/admin/cores?action=SWAP&core=core1&other=core2` | GET | `curl "http://localhost:8983/solr/admin/cores?action=SWAP&core=core1&other=core2"` | Menukar core |

### 1.2 Collection Management (SolrCloud, v2 API)
| Endpoint | Method | Contoh Request | Deskripsi |
|----------|--------|----------------|-----------|
| `/api/collections?action=CREATE&name=mycollection&numShards=2&replicationFactor=2` | POST | `curl -X POST "http://localhost:8983/solr/api/collections?action=CREATE&name=mycollection&numShards=2&replicationFactor=2"` | Membuat collection baru |
| `/api/collections?action=DELETE&name=mycollection` | POST | `curl -X POST "http://localhost:8983/solr/api/collections?action=DELETE&name=mycollection"` | Menghapus collection |
| `/api/collections?action=RELOAD&name=mycollection` | POST | `curl -X POST "http://localhost:8983/solr/api/collections?action=RELOAD&name=mycollection"` | Reload collection |
| `/api/collections?action=BACKUP&name=mycollection&location=/backup` | POST | `curl -X POST "http://localhost:8983/solr/api/collections?action=BACKUP&name=mycollection&location=/backup"` | Backup collection |
| `/api/collections?action=RESTORE&name=mycollection&location=/backup` | POST | `curl -X POST "http://localhost:8983/solr/api/collections?action=RESTORE&name=mycollection&location=/backup"` | Restore collection |

---

## 2️⃣ Indexing / Update / Delete Documents

| Endpoint | Method | Contoh Request | Deskripsi |
|----------|--------|----------------|-----------|
| `/solr/mycore/update` | POST | `curl http://localhost:8983/solr/mycore/update -H 'Content-Type: application/json' -d '[{"id":"1","title":"Mobil Listrik 2025"}]'` | Tambah / update dokumen |
| `/solr/mycore/update?commit=true` | POST | `curl http://localhost:8983/solr/mycore/update?commit=true -H 'Content-Type: application/json' -d '[{"id":"1","title":"Mobil Listrik 2025"}]'` | Commit otomatis |
| `/solr/mycore/update/json/docs` | POST | `curl http://localhost:8983/solr/mycore/update/json/docs -H 'Content-Type: application/json' -d '[{"id":"1","title":"Mobil Listrik 2025"}]'` | Insert JSON docs satu per satu |
| `/solr/mycore/update?optimize=true` | POST | `curl "http://localhost:8983/solr/mycore/update?optimize=true"` | Optimasi index |

---

## 3️⃣ Query / Search

### 3.1 Keyword Search
| Endpoint | Method | Contoh Request | Deskripsi |
|----------|--------|----------------|-----------|
| `/solr/mycore/select?q=mobil+listrik` | GET | `curl "http://localhost:8983/solr/mycore/select?q=mobil+listrik"` | Keyword search standar |
| `/solr/mycore/select?q=mobil+listrik&fl=id,title` | GET | `curl "http://localhost:8983/solr/mycore/select?q=mobil+listrik&fl=id,title"` | Pilih field tertentu |
| `/solr/mycore/select?q=mobil+listrik&start=0&rows=10` | GET | Pagination |
| `/solr/mycore/select?q=mobil+listrik&fq=year:2025` | GET | Filter query |
| `/solr/mycore/select?q=mobil+listrik&sort=title+asc` | GET | Sorting hasil |
| `/solr/mycore/select?q=mobil+listrik&facet=true&facet.field=brand` | GET | Faceting |
| `/solr/mycore/select?q=mobil+listrik&hl=true&hl.fl=title,description` | GET | Highlight kata yang cocok |

### 3.2 Real-Time Get & MoreLikeThis
| Endpoint | Method | Contoh Request | Deskripsi |
|----------|--------|----------------|-----------|
| `/solr/mycore/get?id=1` | GET | `curl "http://localhost:8983/solr/mycore/get?id=1"` | Ambil dokumen berdasarkan ID |
| `/solr/mycore/mlt?q=title:Mobil&mlt.fl=description&mlt.mindf=1&mlt.minwl=3` | GET | MoreLikeThis |

---

## 4️⃣ Neural / Vector Search (Solr 9.3+)

| Endpoint | Method | Contoh Request | Deskripsi |
|----------|--------|----------------|-----------|
| `/solr/mycore/select` | POST | `curl http://localhost:8983/solr/mycore/select -H 'Content-Type: application/json' -d '{"query":"*:*","knn":{"field":"embedding","vector":[0.1,0.2,0.3,...,0.768],"k":10}}'` | Pencarian vektor (KNN) |
| `/solr/mycore/select` | POST | `curl -X POST -H 'Content-Type: application/json' -d '{"query":"mobil listrik","knn":{"field":"embedding","vector":[0.1,0.2,0.3,...,0.768],"k":10}}'` | Hybrid search (keyword + vector) |

---

## 5️⃣ Schema & Config Management

| Endpoint | Method | Contoh Request | Deskripsi |
|----------|--------|----------------|-----------|
| `/solr/mycore/schema` | GET | `curl "http://localhost:8983/solr/mycore/schema"` | Lihat schema |
| `/solr/mycore/schema/fields` | GET | Lihat semua field |
| `/solr/mycore/schema/fields` | POST | Tambah field baru |
| `/solr/mycore/schema/fieldtypes` | GET | Lihat tipe field |
| `/solr/mycore/schema/fieldtypes` | POST | Tambah field type |
| `/solr/mycore/schema/dynamicfields` | GET | Lihat dynamic fields |
| `/solr/mycore/schema/analysis/field` | POST | Analisis tokenizer, stemming, stopwords |

---

## 6️⃣ Admin / Metrics / Logging / Backup

| Endpoint | Method | Contoh Request | Deskripsi |
|----------|--------|----------------|-----------|
| `/solr/admin/info/system` | GET | Info sistem |
| `/solr/admin/info/cores` | GET | Info semua core |
| `/solr/admin/info/properties` | GET | Konfigurasi Solr |
| `/solr/admin/metrics` | GET | Metrics / performa |
| `/solr/admin/logging` | GET/POST | Atur level logging |
| `/solr/admin/replication?command=backup` | GET | Backup core |
| `/solr/admin/replication?command=restore` | GET | Restore core |

---

## 🔹 Ringkasan

- **Core / Collection Management** → create, reload, unload, backup/restore  
- **Indexing / Update / Delete** → insert, update, delete, commit, optimize  
- **Query / Search** → keyword, filter, faceting, highlight, real-time get, MoreLikeThis  
- **Neural / Vector Search** → KNN, hybrid search  
- **Schema / ConfigSet Management** → fields, dynamic fields, types, analyzer  
- **Admin / Metrics / Logging / Backup** → monitoring, backup, restore, logging
