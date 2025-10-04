# Tahap 1: Build aplication
# Menggunakan image Node.js versi 20-alpine sebagai basis builder
FROM node:20-alpine AS builder

# Menetapkan direktori kerja di dalam container
WORKDIR /app

# Menyalin package.json dan package-lock.json terlebih dahulu
# Ini memanfaatkan cache Docker agar tidak mengulang 'npm install' jika tidak ada perubahan dependensi
COPY package*.json ./

# Menginstal semua dependensi proyek
RUN npm install

# Menyalin sisa source code aplikasi
COPY . .

# Menjalankan script build untuk menghasilkan file statis
RUN npm run build

# ---

# Tahap 2: Serve application menggunakan Nginx
# Menggunakan image Nginx yang ringan sebagai basis production
FROM nginx:stable-alpine

# Menyalin hasil build dari tahap 'builder' ke direktori web server Nginx
COPY --from=builder /app/dist /usr/share/nginx/html

# Memberi tahu Docker bahwa container akan berjalan di port 80
EXPOSE 80

# Perintah default untuk menjalankan Nginx saat container dimulai
CMD ["nginx", "-g", "daemon off;"]