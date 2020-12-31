## Hi, we are disease epidemiology lab at NC State University

### GitHub Stats

package main

import (
	"encoding/json"
	"fmt"
	"io/ioutil"
	"net/http"
)

type Org struct {
	Name string `json:"login"`
}

type Repo struct {
	Name  string `json:"full_name"`
	Stars int    `json:"stargazers_count"`
}

func main() {
	url := "https://api.github.com/users/mattetti/orgs"

	resp, err := http.Get(url)
	if err != nil {
		panic(err)
	}
	defer resp.Body.Close()
	body, err := ioutil.ReadAll(resp.Body)
	if err != nil {
		panic(err)
	}

	var stars int

	var orgs []Org
	json.Unmarshal(body, &orgs)
	for _, v := range orgs {

		url = fmt.Sprintf("https://api.github.com/orgs/%s/repos", v.Name)

		resp, err = http.Get(url)
		if err != nil {
			panic(err)
		}
		defer resp.Body.Close()
		body, err = ioutil.ReadAll(resp.Body)
		if err != nil {
			panic(err)
		}

		var repos []Repo
		json.Unmarshal(body, &repos)

		for _, v := range repos {
			fmt.Println(v.Name, v.Stars)
			stars += v.Stars
		}
	}
	fmt.Println("-----------------------")
	fmt.Println("### Total stars:", stars)
}
