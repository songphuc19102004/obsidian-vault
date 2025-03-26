Khi design database, thì phải quan tâm tới tính mở rộng, hiệu suật của câu query(select,…)

nên để id trong tất cả bảng(kể cả bảng phụ của many-many)

many - many: tạo bảng phụ

![[/Untitled 138.png|Untitled 138.png]]

FROM: Luôn ưu tiên, lấy từ table nào, ghi đầu tiên, nên đặt alias

Sau đó JOIN

WHERE: Filter bớt thông tin cần JOIN

GROUP BY: Kiếm thằng nào giống nhau, và group lại rồi đếm nó

HAVING: Điều kiện cho aggregate

  

bt: viet query tu cau 1 → 10