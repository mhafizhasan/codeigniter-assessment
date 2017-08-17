# Codeigniter 3 Assessment


These assessments questions is designed to evaluate developer's understanding on the framework. To answer the questions, you may fork this repo and answer it in this codeigniter installation.

#### Question 1
Explain how to create a static page with the given uri `http://{domain}/page/about`.  

#### Answer 1  
create a controller named `Page.php` contains  

```php
// Page.php
class Page extends CI_Controller {

	public function about()
	{
		echo '<h1>About Page</h1>';
	}

}
```


#### Question 2
From solution no 1, explain how to change the uri to `http://{domain}/about`.  

#### Answer 2  
update `application/config/routes.php` to add routing rule as below  

```php
$route['about'] = 'page/about';
```


#### Question 3
Given that you have a database schema as below: 
```mysql 
  CREATE TABLE news (
        id int(11) NOT NULL AUTO_INCREMENT,
        title varchar(128) NOT NULL,
        slug varchar(128) NOT NULL,
        text text NOT NULL,
        PRIMARY KEY (id),
        KEY slug (slug)
);
```

Explain how would you query a record and display it to the user when visiting the uri `http://{domain}/news/{slug}` where slug is equivalent to the `slug` column in the table.  

#### Answer 3  
update `application/config/routes.php` to add routing rule as below  

```php
$route['news/(:any)'] = 'page/news/$1';
```  

inside `page` controller 

```php
public function news($slug)
{
	$this->load->database();
	$result = $this->db->where('slug', $slug)->get('news')->row();

	echo '<pre>'.var_dump($result).'</pre>';
}
```


#### Question 4
How can you prevent sql injection when providing for the solution in question 3.  

#### Answer 4  
using active record query


#### Question 5
Explain how to create a page where user can create a news entry into the database.  

#### Answer 5  
create news page  

```php
<?php echo form_open('page/submit_news'); ?>
    Title : <input type="text" name="news_title">
    Text : <textarea name="news_text" rows="8" cols="80"></textarea>
    <button type="submit" name="submit_news">Submit</button>
<?php echo form_close(); ?>
```  

update `page` controller with  

```php
public function submit_news()
{
	$this->load->model('news_model');
	$this->news_model->insert_news();
}
```  

create `News_model.php`  

```php
class News_model extends CI_Model
{
    public function __construct()
    {
        parent::__construct();
    }

    public function insert_news()
    {
        $title = $this->input->post('news_title');

        // generate slug for title using regex
        $slug = preg_replace('~[^\pL\d]+~u', '-', $title);

        $data = array(
            'title' => $title,
            'slug' => $slug,
            'text' => $this->input->post('news_text')
        );

        $this->db->insert('news', $data);
    }

}
```

#### Question 6
Explain how can you validate input from user in question 5.  

#### Answer 6  
create `validation rules` using `form_validation` library  


#### Question 7
Explain how can you save a user session when a user has logged in and how to retrieve the session.  

#### Answer 7  
using `session` library  
use `$this->session->set_userdata('username', 'ahmad');` to set session   
use `$this->session->userdata('username');` to retrieve the session  

#### Question 8
Show how can you prevent file upload with size not more than 2MB and only to accept `pdf` file.  

#### Answer 8  
overwrite `upload` library rules as below  

```php
$config['max_size']             = 2048;
$config['allowed_types']        = 'pdf';

$this->load->library('upload', $config);
```

#### Question 9
Based on the schema in question 3, show how you write a migration class to create the table using codeigniter.  

#### Answer 9  
create migration class as below  

```php
class Migration_Add_news extends CI_Migration {

        public function up()
        {
                $this->dbforge->add_field(array(
                        'id' => array(
                                'type' => 'INT',
                                'constraint' => 11,
                                'unsigned' => TRUE,
                                'auto_increment' => TRUE
                        ),
                        'title' => array(
                                'type' => 'VARCHAR',
                                'constraint' => '128',
                        ),
												'slug' => array(
                                'type' => 'VARCHAR',
                                'constraint' => '128',
                        ),
                        'text' => array(
                                'type' => 'TEXT'
                        ),
                ));
                $this->dbforge->add_key('slug', TRUE);
                $this->dbforge->create_table('news');
        }

        public function down()
        {
                $this->dbforge->drop_table('news');
        }
}
```

#### Question 10
What do you think of codeigniter compared to other PHP frameworks available e.g Laravel, Symfony, CakePHP  

#### Answer 10  
a good mvc framework for starter
