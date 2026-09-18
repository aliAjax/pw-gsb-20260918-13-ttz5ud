# 手摇风琴纸带打孔API

纯后端零依赖Node服务，使用 `data/db.json` 持久化曲目、纸带区间和试奏问题。

## 启动

```bash
PORT=3019 node server.js
```

## 主要接口

- `GET /health`
- `GET /tunes`
- `POST /tunes`
- `POST /tunes/:id/copy`
- `GET /tunes/:id/progress`
- `GET /tunes/:id/sections`
- `POST /tunes/:id/sections`
- `GET /tunes/:id/unchecked-sections`
- `PATCH /sections/:id/check`
- `GET /issues?tuneId=&status=`
- `POST /issues`
- `PATCH /issues/:id/status`

## 复制曲目

`POST /tunes/:id/copy`，请求体 `{"title":"新标题"}`：

- 仅当原曲目全部区间已校对且没有未解决问题时才可复制，任一条件不满足则整次拒绝（409）且不落盘
- 新标题必须未被占用，否则返回 409
- 区间与已解决问题一并复制并分配新编号，复制出的区间全部回到未校对，未解决问题不复制
- 原曲目数据保持不变，并发复制串行执行，不会重复或互相覆盖

## 闭环示例

```bash
curl http://127.0.0.1:3019/tunes/tune_demo/progress
curl -X POST http://127.0.0.1:3019/issues \
  -H 'Content-Type: application/json' \
  -d '{"tuneId":"tune_demo","sectionId":"section_demo_2","type":"错孔","beat":45,"lane":9,"description":"第45拍第9轨多打孔"}'
```
