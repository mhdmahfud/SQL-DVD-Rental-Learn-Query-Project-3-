# SQL-DVD-Rental-Learn-Query-Project-3
![DVD Rental Schema](https://i.ibb.co/xxxx/schema.png)

	--Basic SQL WHERE

-- LIVE CODING EXERCISE - BASIC SQL & WHERE

-- 1. Skenario Casting Cell
--  Tim casting mencari aktor dengan nama belakang  berawalan huruf " A"
-- Tugas Tampilkan first name dan last name dari tabel actor
select 
	a.first_name, 
	a.last_name 
from actor a 
where a.last_name like 'A%'

-- 2 Skenario: Analisi film modern
-- kita hanya membutuhkan data film terbaru.
--Tugas : Tampilkan film yang dirilis(release_year) setelah tahun 2025
select 
	f.film_id ,
	f.title ,
	f.release_year  
from film f 
where f.release_year > 2005;

--3. Skenario : Audit Akun
-- tim marketing ingin mengirim email ke pelanggan aktif saja.
-- tugas : tampilkan ID dengan nama pelanggan yang berstatusnya aktif(active = 1)
select 
	c.customer_id,
	concat(c.first_name,' ',c.last_name) customer_name,
	c.active 
from customer c 
where c.active <= 1;

-- 4. skenario: Promosi Harga
--Kita ingin melihat film dengan harga sewa spesifik.
--Tugas: Ciri Judul film dengan rental_rate tepat di angkka 0.99 atau 2.99
select 
	f.film_id,
	f.title,
	f.rental_rate  
from film f  
where f.rental_rate = 0.99 or f.rental_rate =2.99;

select 
	f.film_id ,
	f.title ,
	f.rental_rate 
from film f 
where f.rental_rate in(0.99, 2.99)	


-- 5. Skenario: Validasi Data
-- Membersihkan data alamat yang tidak lengkap
-- Tampilkan alamat yang kolom address2nya tidak kosong(Not Null)
select *
from address a 
where a.address2 is not null;



---------------------------------------------------------------------------
---------------------------------------------------------------------------
---------------------------------------------------------------------------


-- Live Coding Exercise - Aggregation & Group By


-- 1. Skenario Inventaris Rating
-- Berapa banyak stok film untuk setiap rating umur?
--tugas: hitung jumlah total film untuk setiap rating
-- show:
select 
	f.rating,
	count(f.film_id ) as total_film
from film f 
group by f.rating;

--2. Skenario: Revenue Pelanggan
--Siapa pelanggan yang paling banyak berkontribusi?
--Tugas : Hitung total amount pembayaran yang dilakukan oleh setiap customer_id
select 
	c.customer_id ,
	concat(c.first_name , ' ', c.last_name ) as customer_name,
	sum(p.amount) as total_amount
from customer c 
inner join payment p on c.customer_id =p.customer_id 
group by c.customer_id 
order by sum(p.amount) desc ;


--3. Skenario High Spenders
--filter Hanya pelanggan VIP
--Tugas : Tampilkan customer_id yang memiliki total pembayaran di atas 120 (Gunakan Having)
select 
	p.customer_id,
	sum(p.amount ) total_pembayaran
from payment p  
group by customer_id 
having sum(p.amount) > 120;

-- 4. Skenario: Rata- rata harga
-- Analisis harga sewa berdasarkan kategori rating.
--tugas: hitung rata-rata rental rate untuk setiap kategori rating.
select 
	f1.rating,
	avg(f1.rental_rate) avg_rentalrate
from film f1
group by  f1.rating
order by avg(f1.rental_rate) desc;

-- 5. Skenario: KPI Staf
-- Mengukur Porduktivitas Karyawan.
--Tugas: Hitung berapa kali setiap staf(staff_id) memproses rental

select  
	r.staff_id,
	count(r.rental_id ) jumlah_rental
from rental r  
group by r.staff_id ;



---------------------------------------------------------------------------
 ---------------------------------------------------------------------------
---------------------------------------------------------------------------


--LIVE CODING EXERCISE - JOIN(SINGLE & MULTI TABLE)

-- 1. Skenario: katalog internasional(2 tabel)
-- Tugas: tampilkan judul film beserta nama bahasanya (language)
select 
	f.film_id, 
	f.title,
	l."name" as language_film
from film f 
join "language" l on f.language_id = l.language_id ;

select distinct l."name"  from "language" l 


--2. Skenario: aktegori film (3 table)
-- Tugas: Tampilkan judul film(film) dan nama kategorinya (category)
--  int: gunakan tabel perantara
select 
	f.film_id,
	c.category_id,
	c."name",
	f.title
from film f
inner join film_category fc on f.film_id  = fc.film_id 
inner join category c on  fc.category_id  = c.category_id  
;

select * from film_category fc  ;

-- 3. Skenario : Laporan peminjaman(4 Tabel)
-- Tugas: Tampilkan nama customer, judul film yang di pinjam dan tanggal rental.
--(path: customer > rental > inventory > film)
select 
	concat(c.first_name , ' ', c.last_name ) as customer,
	f.title as film_titler,
	r.rental_date 
from customer c
join rental r on c.customer_id = r.customer_id 
join inventory i on i.inventory_id = r.inventory_id 
join film f  on i.film_id = f.film_id 
;

--4. skenario : audit lokasi staff(4 tabel)
--Tugas: Tampilkan nama staff, id Toko, dan nama negara tempat toko berada.
-- Path: Staff > store > address > city >country
 select 
	concat(s2.first_name ,' ', s2.last_name ) as staf_name,
	s.store_id ,
	c2.country 
from store s 
join staff s2  on s.store_id = s2.store_id 
join address a  on s2.address_id = a.address_id 
join city c on a.city_id = c.city_id 
join country c2 on c.country_id = c2.country_id 
;

-- 5. Skenario: Portofolio Aktor (5 Tabel)
-- Tugas : Tampilkan nama, judul film yang di bintangi dan kategori film tersebut
select 
	concat(a.first_name ,' ', a.last_name ) as actor_name,
	f.title as film_title,
	c."name" as film_category
from actor a 
join film_actor fa on a.actor_id = fa.actor_id 
join film f on fa.film_id = f.film_id 
join film_category fc on f.film_id = fc.film_id 
join category c on fc.category_id = c.category_id ;


-----------------------------------------------------------------
-----------------------------------------------------------------
-----------------------------------------------------------------


--Live coding Excercide - Subquery

-- 1. Skenario : Banchmarking Harga. mencari film di atas harga pasar.
--Tugas : Tampilkan Film yang harga sewanya (Rental_rate) lebih tinggi dari rata-rata harga sewa seluruh film

select 
	f.film_id,
	f.title,
	f.rental_rate as 
from film f  
where f.rental_rate > 
	(select avg(f2.rental_rate) as avg_rental_rate
	from film f2 ) 
; 


-- 2. Durasi Ekstrem. Mencari film terpanjang.
-- Tugas: Tampilkan film yang durasi sama dengan durasi film terpanjang di database

select 
	f.film_id,
	f.title,
	f.length 
from film f 
where f.length = (select max(f2.length ) as max_length from film f2  ) ;


-- 3. skenario: Aktor Produktif. Filter aktor berpengalaman
-- Tugas: Tampilkan ID aktor yang telat membintangi lebih dari 3 film(Gunakan subquery pada logic filter/Having)
select 
	a.actor_id,
	a.first_name ,
	a.last_name 
from actor a 
join film_actor fa  on a.actor_id  = fa.actor_id 
join  film f on fa.film_id  = f.film_id 
group by a.actor_id 
having count(a.actor_id ) > 3;

-- 4. Pelanggan Aktif Rental. Validasi data asing.
-- Tugas: Tampilkan detail data pelnggan yang id-nya ada di dalam tabel rental(gunakan IN)
select 
	*
from customer c 
where c.customer_id in (select r.customer_id  from rental r  )
; 

-- 5. Skenario: pengeluaran di atas rata-rata. analisis performa belanja.
-- Tugas: Tampilkan customer yang total pembayarannya lebih besar dari rata-rata total pembayaran semua customer( Nested Subquery)

SELECT 
    c.customer_id,
    c.first_name,
    c.last_name,
    (SELECT SUM(p.amount) 
     FROM payment p 
     WHERE p.customer_id = c.customer_id) as total_pembayaran
FROM 
    customer c
WHERE 
    (SELECT SUM(p.amount) 
     FROM payment p 
     WHERE p.customer_id = c.customer_id) > 
    (SELECT AVG(p2.amount) 
     FROM payment p2);

----------------------------------------------------------------------
-- LIVE CODING EXERCISE - WINDOW FUNCTIONS
--  Skenario: Ranking Film.
-- Tugas. Berikan peringkatan(RANK= ada gap) pada film berdasarkan "rental_rate" tertinggi ke terendah
select
	f.film_id,
	f.title ,
	f.rental_rate ,
	rank() over (order by f.rental_rate desc ) as rental_rank
from film f ;



-- Skenario: Leaderboard Pelanggan.
-- Tugas: berikan perinkat (DENSE_RANK= Tidak Ada gap) kepada pelanggan berdasarkan total pembayaran mereka.

SELECT 
    f.film_id,
    f.title,
    f.rental_rate,
    DENSE_RANK() OVER (ORDER BY sum(f.rental_rate) DESC) AS rental_rank
FROM 
    film f
group by f.film_id , f.title ;

-- Skenario Nomor urut rilis.
--Tugas: buat nomor baris(row_number) untuk setiap film di urutkan berdasaarkan tahun rilis
select 
	f.film_id,
	f.title ,
	f.release_year ,
	row_number() over (order by f.release_year desc)
from film f ;


-- Skenario: Komparasi Pembayaran.
--Tugas: Tamplikan data pembayaran per transaksi, disandingkan dengan total pembayaran pelanggan tersebut(SUM dengan partition by)
select 
	p.payment_id ,
	p.customer_id,
	p.amount,
	sum(p.amount) over (partition by customer_id) as total_pembayaran,
	rank() over(partition by p.customer_id  order by p.amount desc  ) as rank_per_customer
from payment p 
;

SELECT 
    p.payment_id,
    p.customer_id,
    p.amount,
    SUM(p.amount) OVER (PARTITION BY p.customer_id) AS total_pembayaran,
    RANK() OVER (PARTITION BY p.customer_id ORDER BY p.amount DESC) AS rank_per_customer
FROM 
    payment p;

--Skenario: Transaksi Terbesar.
-- Tuags: Gunakan Subquery dan windows fanction untuk hanyak mengambil transaksi pembayaran terbesar(Ranking 1) untuk setipa customer

WITH ranked_payments AS (
    SELECT 
        p.payment_id,
        p.customer_id,
        p.amount,
        DENSE_RANK() OVER (PARTITION BY p.customer_id ORDER BY p.amount DESC) AS rank_transaksi
    FROM 
        payment p
)
SELECT 
    payment_id,
    customer_id,
    amount,
    rank_transaksi
FROM 
    ranked_payments
WHERE 
    rank_transaksi = 1;

-- LIVE CODING EXERCISE -  CTE & CLASSIFICATION
--CTE(COMMN table EXPRESSIONS)

--1. Skenario: pelanggan setia.
--tugas: gunakan CTE untuk menyaring pelanggan yang memiliki lebih dari 15 transaksi rental 
 with customer_transaktion as(
	select 
		r.customer_id ,
		count(r.rental_id ) total_rental
	from rental r
	group by r.customer_id 
 ) 
select * 
from customer_transaktion 
where total_rental > 15;


-- 2. Skenario:Film populer.
-- Tugas : Gunakan CTE untuk menghitung  berapa kali sebuah film di rental, lalu join ke table film untuk menampilkan judulnya
with RENTAL_COUNT AS(
	select 
		f.film_id, 
		f.title, 
		count(r.rental_id ) as rental_film 
	from film f  
	join inventory i on f.film_id = i.film_id  
	join rental r on i.inventory_id = r.inventory_id 
	group by f.film_id , f.title 
)
select film_id , title  ,rental_film  from RENTAL_COUNT 


--CASE WHEN

-- 3. Skenario: Lebeling Harga
-- Tugas : Buat kolom baru 'Kategori Harga': jika > 4 ' Premium', 2-4 'Regular', sisanya 'budget'.

select 
	p.payment_id ,
	p.amount ,
	case 
		when p.amount  > 4 then 'Premium'
		when p.amount  between 2 and 4 then ' Regular'
		else 'budget'
	end as kategori_harga
from payment p ;



--4. Skenario: Segmentasi Pelanggan.
--Tugas: Kategorical pelanggan berdasarkan total spend: > 200 'high value', 100-200 'Medium', Sisanya 'Low'

select 
	p.customer_id ,
	sum(p.amount) total_spend,
	case
		when sum(p.amount) > 200  then 'High Vaue'
		when sum(p.amount) between 100 and 200 then 'Mediium'
		else 'Low'
	end as Segmnet
from payment p 
group by p.customer_id
order by total_spend 	 desc;


----------------------------------------------------------------
