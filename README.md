import os
import time
import markdown
import json
from datetime import datetime

class PortfolioCreator:
    def __init__(self, username, repo_name="data-analyst-portfolio"):
        self.username = username
        self.repo_name = repo_name
        self.repo_path = f"./{repo_name}"
        
    def create_directory_structure(self):
        """Tạo cấu trúc thư mục cho portfolio"""
        print("Đang tạo cấu trúc thư mục portfolio...")
        
        # Tạo thư mục chính
        if not os.path.exists(self.repo_path):
            os.makedirs(self.repo_path)
        
        # Tạo các thư mục con
        subdirs = [
            "projects",
            "projects/data-visualization",
            "projects/data-cleaning",
            "projects/exploratory-analysis",
            "projects/machine-learning",
            "projects/sql-analysis",
            "assets",
            "assets/images",
            "assets/data",
            "certificates",
            "resume"
        ]
        
        for subdir in subdirs:
            path = os.path.join(self.repo_path, subdir)
            if not os.path.exists(path):
                os.makedirs(path)
                print(f"  Đã tạo: {path}")
                
        print("Hoàn thành tạo cấu trúc thư mục!")
        
    def create_readme(self, data):
        """Tạo file README.md chính"""
        print("Đang tạo file README.md...")
        
        readme_content = f"""# {data['name']} - Data Analyst Portfolio

## Giới thiệu
{data['introduction']}

## Kỹ năng
{self._format_skills(data['skills'])}

## Dự án
{self._format_projects(data['projects'])}

## Chứng chỉ
{self._format_certificates(data['certificates'])}

## Học vấn
{self._format_education(data['education'])}

## Liên hệ
- Email: {data['contact']['email']}
- LinkedIn: [{data['contact']['linkedin']}](https://www.linkedin.com/in/{data['contact']['linkedin']})
- GitHub: [{data['contact']['github']}](https://github.com/{data['contact']['github']})

*Cập nhật lần cuối: {datetime.now().strftime('%d/%m/%Y')}*
"""
        
        with open(os.path.join(self.repo_path, "README.md"), "w", encoding="utf-8") as f:
            f.write(readme_content)
            
        print("Đã tạo file README.md!")
        
    def _format_skills(self, skills):
        """Format phần kỹ năng"""
        result = ""
        for category, skill_list in skills.items():
            result += f"### {category}\n"
            result += ", ".join(skill_list)
            result += "\n\n"
        return result
        
    def _format_projects(self, projects):
        """Format phần dự án"""
        result = ""
        for project in projects:
            result += f"### [{project['name']}](./projects/{project['folder']})\n"
            result += f"{project['description']}\n\n"
            result += f"**Công nghệ sử dụng:** {', '.join(project['technologies'])}\n\n"
            
            # Tạo thư mục và file README cho từng dự án
            project_path = os.path.join(self.repo_path, "projects", project['folder'])
            if not os.path.exists(project_path):
                os.makedirs(project_path)
                
            project_readme = f"""# {project['name']}

## Tổng quan
{project['description']}

## Mục tiêu
{project['objective']}

## Dữ liệu
{project['data_source']}

## Phương pháp
{project['methodology']}

## Kết quả
{project['results']}

## Công nghệ sử dụng
{', '.join(project['technologies'])}
"""
            with open(os.path.join(project_path, "README.md"), "w", encoding="utf-8") as f:
                f.write(project_readme)
                
        return result
        
    def _format_certificates(self, certificates):
        """Format phần chứng chỉ"""
        result = ""
        for cert in certificates:
            result += f"- **{cert['name']}** - {cert['issuer']} ({cert['date']})\n"
        return result
        
    def _format_education(self, education):
        """Format phần học vấn"""
        result = ""
        for edu in education:
            result += f"- **{edu['degree']}** - {edu['institution']} ({edu['year']})\n"
        return result
        
    def create_git_init_script(self):
        """Tạo file script để khởi tạo Git repository"""
        script_content = f"""#!/bin/bash
cd {self.repo_path}
git init
git add .
git commit -m "Initial commit: Portfolio setup"
git branch -M main
git remote add origin https://github.com/{self.username}/{self.repo_name}.git
git push -u origin main
"""
        
        with open(os.path.join(self.repo_path, "git_init.sh"), "w") as f:
            f.write(script_content)
            
        print("Đã tạo file git_init.sh!")
        
    def create_sample_data(self):
        """Tạo dữ liệu mẫu cho portfolio"""
        data = {
            "name": "Họ Tên Của Bạn",
            "introduction": "Tôi là một Data Analyst với hơn X năm kinh nghiệm trong việc phân tích dữ liệu, trực quan hóa thông tin và cung cấp insights có giá trị để hỗ trợ ra quyết định. Tôi có kiến thức sâu về các công cụ phân tích dữ liệu như Python, R, SQL, và các nền tảng BI như Tableau và Power BI.",
            "skills": {
                "Ngôn ngữ lập trình": ["Python", "R", "SQL"],
                "Công cụ phân tích dữ liệu": ["Pandas", "NumPy", "Matplotlib", "Seaborn", "Scikit-learn"],
                "Trực quan hóa dữ liệu": ["Tableau", "Power BI", "Plotly", "D3.js"],
                "Cơ sở dữ liệu": ["MySQL", "PostgreSQL", "MongoDB", "SQLite"],
                "Phân tích và Thống kê": ["Phân tích dữ liệu thăm dò", "Kiểm định giả thuyết", "Phân tích tương quan", "A/B Testing"],
                "Công cụ khác": ["Git", "Jupyter Notebook", "Excel", "Google Sheets", "AWS", "Docker"]
            },
            "projects": [
                {
                    "name": "Phân tích xu hướng bán hàng",
                    "folder": "sales-trend-analysis",
                    "description": "Phân tích dữ liệu bán hàng 5 năm để xác định các xu hướng, mẫu mua hàng theo mùa và cơ hội tăng trưởng.",
                    "objective": "Xác định xu hướng bán hàng và đưa ra các khuyến nghị để tăng doanh số.",
                    "data_source": "Dữ liệu bán hàng từ hệ thống ERP (2018-2023)",
                    "methodology": "Sử dụng Python (Pandas, Matplotlib) để phân tích dữ liệu và trực quan hóa xu hướng theo thời gian.",
                    "results": "Phát hiện ra xu hướng mua hàng theo mùa và đề xuất chiến lược tiếp thị đã giúp tăng doanh số 15%.",
                    "technologies": ["Python", "Pandas", "Matplotlib", "SQL", "Tableau"]
                },
                {
                    "name": "Phân tích dữ liệu COVID-19",
                    "folder": "covid19-analysis",
                    "description": "Phân tích dữ liệu COVID-19 toàn cầu để theo dõi xu hướng lây nhiễm và tác động của các biện pháp y tế công cộng.",
                    "objective": "Phân tích xu hướng lây nhiễm COVID-19 và đánh giá hiệu quả của các biện pháp phòng ngừa.",
                    "data_source": "Dữ liệu công khai từ Johns Hopkins University và WHO",
                    "methodology": "Sử dụng Python và R để phân tích dữ liệu, xây dựng mô hình dự báo và tạo dashboard trực quan.",
                    "results": "Phát hiện mối tương quan giữa các biện pháp phong tỏa và giảm tỷ lệ lây nhiễm.",
                    "technologies": ["R", "Python", "ggplot2", "Plotly", "Shiny"]
                },
                {
                    "name": "Phân tích hành vi người dùng",
                    "folder": "user-behavior-analysis",
                    "description": "Phân tích hành vi người dùng trên nền tảng thương mại điện tử để tối ưu hóa trải nghiệm và tăng tỷ lệ chuyển đổi.",
                    "objective": "Phân tích hành vi người dùng và đề xuất cải tiến UX/UI.",
                    "data_source": "Dữ liệu click-stream và dữ liệu Google Analytics",
                    "methodology": "Sử dụng Python và SQL để phân tích dữ liệu, xây dựng phễu chuyển đổi và bản đồ nhiệt.",
                    "results": "Xác định các điểm nghẽn trong quy trình mua hàng và đề xuất thay đổi đã tăng tỷ lệ chuyển đổi 18%.",
                    "technologies": ["Python", "SQL", "Google Analytics", "Power BI"]
                }
            ],
            "certificates": [
                {
                    "name": "Google Data Analytics Professional Certificate",
                    "issuer": "Google",
                    "date": "2022"
                },
                {
                    "name": "Data Science Professional Certificate",
                    "issuer": "IBM",
                    "date": "2021"
                },
                {
                    "name": "Machine Learning Specialization",
                    "issuer": "Stanford University & Coursera",
                    "date": "2023"
                }
            ],
            "education": [
                {
                    "degree": "Thạc sĩ Khoa học Dữ liệu",
                    "institution": "Trường Đại học XYZ",
                    "year": "2020-2022"
                },
                {
                    "degree": "Cử nhân Khoa học Máy tính",
                    "institution": "Trường Đại học ABC",
                    "year": "2016-2020"
                }
            ],
            "contact": {
                "email": "email@example.com",
                "linkedin": "your-linkedin-username",
                "github": self.username
            }
        }
        
        # Lưu dữ liệu mẫu vào file JSON
        with open(os.path.join(self.repo_path, "portfolio_data.json"), "w", encoding="utf-8") as f:
            json.dump(data, f, indent=2, ensure_ascii=False)
            
        print("Đã tạo file portfolio_data.json với dữ liệu mẫu!")
        return data
        
    def create_portfolio(self):
        """Tạo toàn bộ portfolio"""
        print(f"Bắt đầu tạo portfolio cho {self.username}...")
        self.create_directory_structure()
        data = self.create_sample_data()
        self.create_readme(data)
        self.create_git_init_script()
        print(f"Portfolio đã được tạo thành công trong thư mục {self.repo_path}!")
        print("Bước tiếp theo: Tùy chỉnh dữ liệu trong portfolio_data.json và đẩy lên GitHub")

if __name__ == "__main__":
    username = input("Nhập tên người dùng GitHub của bạn: ")
    repo_name = input("Nhập tên repository (mặc định: data-analyst-portfolio): ") or "data-analyst-portfolio"
    
    portfolio_creator = PortfolioCreator(username, repo_name)
    portfolio_creator.create_portfolio()