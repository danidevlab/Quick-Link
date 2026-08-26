# Quick-Link
Quick Link template.

## 세팅 가이드

[Supabase 대시보드](https://supabase.com)에서 단축 링크 서비스를 위한 데이터베이스와 보안 설정을 단 한 번의 SQL 실행으로 완료하는 방법입니다.

---

### 1. 프로젝트 생성 및 API 키 확인

1. [Supabase](https://supabase.com)에 로그인 후 **New Project**를 클릭해 새 프로젝트를 생성합니다.
2. 좌측 하단 **Settings** ➔ **API** 메뉴로 이동합니다.
3. 다음 두 가지 정보를 복사하여 index.html의 47번 줄과 48번 줄, 404.html의 10번 줄과 11번 줄을 실제 값으로 번경하세요.
   * **Project URL** (`SUPABASE_URL`)
   * **Project API keys**의 `anon` / `public` (`SUPABASE_ANON_KEY`)

---

### 2. SQL 쿼리 실행

1. Supabase 대시보드 좌측 메뉴에서 **SQL Editor**로 이동합니다.
2. **New Query**를 누르고 아래의 전체 코드를 복사해서 붙여넣습니다.
3. **Run** 버튼을 누르면 테이블 생성, 보안(RLS) 설정, 샘플 데이터 입력까지 한 번에 완료됩니다.

```sql
-- 1. 단축 링크 테이블 생성
CREATE TABLE IF NOT EXISTS short_links (
    id BIGINT PRIMARY KEY GENERATED ALWAYS AS IDENTITY,
    short_code TEXT NOT NULL UNIQUE,
    target_url TEXT NOT NULL,
    created_at TIMESTAMPTZ DEFAULT NOW()
);

-- 2. Row Level Security(RLS) 활성화
ALTER TABLE short_links ENABLE ROW LEVEL SECURITY;

-- 3. 누구나 조회(SELECT)할 수 있도록 정책 허용
CREATE POLICY "Allow public read access" 
ON short_links 
FOR SELECT 
USING (true);

-- 4. 누구나 등록(INSERT)할 수 있도록 정책 허용
CREATE POLICY "Allow public insert access" 
ON short_links 
FOR INSERT 
WITH CHECK (true);

-- 5. 테스트용 샘플 데이터 삽입 (선택 사항)
INSERT INTO short_links (short_code, target_url) 
VALUES 
    ('google', 'https://google.com'),
    ('github', 'https://github.com')
ON CONFLICT (short_code) DO NOTHING;
```