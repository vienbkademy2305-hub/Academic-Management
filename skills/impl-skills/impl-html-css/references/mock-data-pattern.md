# HTML/CSS — Mock Data (localStorage) Layer Pattern

Template dùng trong Bước 5 khi tạo `mock-data.js` trong feature folder.
Mọi CRUD trong `script.js` phải gọi qua các hàm ở đây — không đọc/ghi `localStorage` trực tiếp ở nơi khác.

```js
// {feature-name}/mock-data.js
// MOCK — dữ liệu lưu tại localStorage, thay bằng API thực khi tích hợp backend

const STORAGE_KEY = "{project}_{feature}_{entity}"; // namespaced để tránh đụng key giữa các feature

// Seed data (từ BA Spec Section 3 hoặc common sense) — chỉ ghi khi key chưa tồn tại
const SEED_DATA = [
  // { id: "1", /* fields theo BA Spec */ },
];

function seedIfEmpty() {
  if (localStorage.getItem(STORAGE_KEY) === null) {
    localStorage.setItem(STORAGE_KEY, JSON.stringify(SEED_DATA));
  }
}

function readAll() {
  seedIfEmpty();
  try {
    return JSON.parse(localStorage.getItem(STORAGE_KEY)) ?? [];
  } catch (err) {
    console.error(`[mock-data] Failed to parse ${STORAGE_KEY}`, err);
    return [];
  }
}

function writeAll(items) {
  localStorage.setItem(STORAGE_KEY, JSON.stringify(items));
}

// --- Public CRUD API ---

export function getAll() {
  return readAll();
}

export function getById(id) {
  return readAll().find((item) => item.id === id) ?? null;
}

export function create(entry) {
  const items = readAll();
  const newItem = { ...entry, id: crypto.randomUUID() };
  items.push(newItem);
  writeAll(items);
  return newItem;
}

export function update(id, patch) {
  const items = readAll();
  const index = items.findIndex((item) => item.id === id);
  if (index === -1) return null;
  items[index] = { ...items[index], ...patch };
  writeAll(items);
  return items[index];
}

export function remove(id) {
  const items = readAll();
  const filtered = items.filter((item) => item.id !== id);
  writeAll(filtered);
  return filtered.length !== items.length;
}

export function resetSeed() {
  localStorage.setItem(STORAGE_KEY, JSON.stringify(SEED_DATA));
}
```

## Usage trong script.js

```js
// {feature-name}/script.js
import { getAll, create, update, remove } from "./mock-data.js";

function render() {
  const items = getAll();
  if (items.length === 0) {
    renderEmptyState();
    return;
  }
  renderList(items);
}

document.addEventListener("DOMContentLoaded", render);
```

## Quy tắc bắt buộc

```
□ Mỗi feature có đúng 1 mock-data.js — không trộn nhiều entity không liên quan vào 1 key
□ STORAGE_KEY namespaced theo `{project}_{feature}_{entity}` — tránh xung đột giữa các feature
□ Luôn seedIfEmpty() trước khi đọc — tránh trả về null gây lỗi render
□ CRUD function name rõ nghĩa: getAll / getById / create / update / remove (không tự đặt tên khác)
□ script.js chỉ import và gọi hàm từ mock-data.js — không gọi localStorage.* trực tiếp
□ Khi cần giả lập độ trễ mạng (để demo loading state) → dùng setTimeout bọc quanh lời gọi mock-data, không sửa mock-data.js
```
